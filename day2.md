# Gravity Forms Training - Day 2 Plan

## Morning Session (9:00 AM - 1:15 PM)

### 9:00 - 9:30 AM: Review & Advanced Form Building
- **Review Day 1 Implementation** (15 min)
  - Examine the customer inquiry form structure
  - Review the custom hook implementation
  - Discuss any questions from day 1

- **Planning Advanced Forms** (15 min)
  - Multi-page form strategy
  - Field organization and grouping
  - Mobile-friendly form considerations  

### 9:30 - 10:30 AM: Advanced Field Types & Functionality
- **Specialty Fields** (30 min)
  - Date and time pickers
  - List fields (dynamic entry rows)
  - Quiz fields
  - Poll fields
  - Survey fields
- **Multi-column Layouts** (15 min)
  - Using CSS ready classes
  - Responsive column configurations
- **Field Visibility & Logic** (15 min)
  - Advanced conditional logic scenarios
  - Field dependency chains
  - Logic based on user roles or page attributes

### 10:30 - 11:30 AM: Form Entry Management
- **Entries Admin Interface** (20 min)
  - Viewing and searching entries
  - Bulk operations on entries
  - Entry details and notes
  - Editing entries
- **Entry Processing** (20 min)
  - Custom entry views
  - Exporting entries (CSV, Excel)
  - Entry approval workflows
- **Entry Hooks & Filters** (20 min)
  - Modifying entry data
  - Custom entry statuses
  - Entry expiration and deletion

### 11:30 AM - 12:30 PM: Integration & Add-ons
- **Popular Gravity Forms Add-ons** (30 min)
  - User Registration
  - MailChimp/Email Marketing
  - PayPal/Stripe
  - Zapier
- **Custom Integration Methods** (30 min)
  - REST API usage
  - Webhook implementations
  - Third-party API connections
  - External database integration

### 12:30 - 1:15 PM: Hands-on Project #1
- **Create a Multi-page Registration Form with Payment** (45 min)
  - Create a 3-page form:
    * Page 1: Personal details
    * Page 2: Service selection with pricing
    * Page 3: Payment information
  - Implement:
    * Progress indicator
    * Save and continue functionality
    * Conditional pricing based on selections
    * Basic PayPal integration (or mock payment gateway)

## Lunch Break (1:15 - 2:00 PM)

## Afternoon Session (2:00 - 3:30 PM)

### 2:00 - 2:45 PM: Advanced Hook Development
- **Advanced Entry Manipulation** (20 min)
  - Modifying entries before and after submission
  - Custom validation rules
  - Field value normalization
- **Custom Form Processing** (25 min)
  - Creating custom database tables for entries
  - Enhanced file handling for uploads
  - Custom reporting functionality

### 2:45 - 3:30 PM: Hands-on Project #2
- **Enhance Custom Hook Implementation** (45 min)
  - Add these features to the existing hook:
    * JSON export option for form submissions
    * Email notification when submissions file reaches certain size
    * Admin dashboard widget showing recent submissions
    * Simple statistics tracking (submissions per form, per day)

## Specific Implementation Details

### Multi-page Registration Form Specifications
1. **Page 1: Personal Information**
   - Name (First & Last) - required
   - Email Address - required with validation
   - Phone Number - optional with format mask
   - Address with autocomplete - required

2. **Page 2: Service Selection**
   - Service Type (Radio Buttons) - required
     * Basic Package ($99)
     * Premium Package ($199)
     * Enterprise Package ($399)
   - Add-ons (Checkboxes) - optional
     * Priority Support (+$50)
     * Extended Warranty (+$75)
     * Custom Branding (+$125)
   - Delivery Timeframe (Dropdown) - required
     * Standard (included)
     * Express (+$45)
     * Rush (+$95)
   - Pricing Summary (HTML field - calculated totals)

3. **Page 3: Payment Information**
   - Payment Method (Radio Buttons) - required
     * Credit Card
     * PayPal
     * Bank Transfer
   - Payment Fields (conditional based on selection)
   - Order Summary (HTML field)
   - Terms and Conditions (Checkbox) - required

### Enhanced Hook Implementation Code

```php
<?php
/**
 * Plugin Name: Advanced Gravity Forms Data Logger
 * Description: Enhanced submission logging with JSON export and statistics
 * Version: 2.0
 * Author: Your Engineer's Name
 */

// Exit if accessed directly
if (!defined('ABSPATH')) {
    exit;
}

class GF_Advanced_Data_Logger {
    
    // Storage directory
    private $storage_dir;
    
    // Max file size before notification (5MB)
    private $max_file_size = 5 * 1024 * 1024;
    
    // Stats array
    private $stats = [];
    
    /**
     * Constructor
     */
    public function __construct() {
        // Set storage directory
        $this->storage_dir = WP_CONTENT_DIR . '/form_submissions';
        
        // Create directory if it doesn't exist
        if (!file_exists($this->storage_dir)) {
            mkdir($this->storage_dir, 0755, true);
        }
        
        // Add hooks
        add_action('gform_after_submission', [$this, 'log_form_submission'], 10, 2);
        add_action('wp_dashboard_setup', [$this, 'add_dashboard_widget']);
        
        // Load stats
        $this->load_stats();
    }
    
    /**
     * Log form submission to file
     */
    public function log_form_submission($entry, $form) {
        // Update stats
        $this->update_stats($form['id']);
        
        // Get form data
        $form_data = $this->prepare_form_data($entry, $form);
        
        // Save as text
        $this->save_as_text($form_data, $form['id']);
        
        // Save as JSON
        $this->save_as_json($form_data, $form['id']);
        
        // Check file size and notify if needed
        $this->check_file_size($form['id']);
    }
    
    /**
     * Prepare form data
     */
    private function prepare_form_data($entry, $form) {
        $data = [
            'metadata' => [
                'timestamp' => date('Y-m-d H:i:s'),
                'form_id' => $form['id'],
                'form_title' => $form['title'],
                'entry_id' => $entry['id'],
                'ip_address' => $entry['ip'],
                'user_agent' => $entry['user_agent']
            ],
            'fields' => []
        ];
        
        // Process fields
        foreach ($form['fields'] as $field) {
            $field_data = $this->process_field($field, $entry);
            if (!empty($field_data)) {
                $data['fields'][] = $field_data;
            }
        }
        
        return $data;
    }
    
    /**
     * Process a single field
     */
    private function process_field($field, $entry) {
        $field_id = $field->id;
        
        // Skip fields with no ID
        if (empty($field_id)) {
            return null;
        }
        
        $result = [
            'id' => $field_id,
            'label' => $field->label,
            'type' => $field->type
        ];
        
        // Handle complex fields with inputs
        if (is_array($field->inputs)) {
            $result['inputs'] = [];
            foreach ($field->inputs as $input) {
                $input_id = $input['id'];
                $input_value = rgar($entry, $input_id);
                if (!empty($input_value) || $input_value === '0') {
                    $result['inputs'][] = [
                        'id' => $input_id,
                        'label' => $input['label'],
                        'value' => $input_value
                    ];
                }
            }
            
            // If no inputs had values, return null
            if (empty($result['inputs'])) {
                return null;
            }
        } else {
            // Simple field
            $value = rgar($entry, $field_id);
            if (empty($value) && $value !== '0') {
                return null;
            }
            $result['value'] = $value;
        }
        
        return $result;
    }
    
    /**
     * Save data as text file
     */
    private function save_as_text($data, $form_id) {
        $filename = "form_{$form_id}_submissions.txt";
        $file_path = $this->storage_dir . '/' . $filename;
        
        // Format text output
        $text = "=== New Submission ===\n";
        $text .= "Time: {$data['metadata']['timestamp']}\n";
        $text .= "Form: {$data['metadata']['form_title']} (ID: {$data['metadata']['form_id']})\n";
        $text .= "Entry ID: {$data['metadata']['entry_id']}\n\n";
        
        // Add fields
        foreach ($data['fields'] as $field) {
            if (isset($field['inputs'])) {
                $input_values = [];
                foreach ($field['inputs'] as $input) {
                    $input_values[] = "{$input['label']}: {$input['value']}";
                }
                $text .= "{$field['label']}: " . implode(", ", $input_values) . "\n";
            } else {
                $text .= "{$field['label']}: {$field['value']}\n";
            }
        }
        
        $text .= "\n======================\n\n";
        
        // Append to file
        file_put_contents($file_path, $text, FILE_APPEND);
    }
    
    /**
     * Save data as JSON
     */
    private function save_as_json($data, $form_id) {
        $date = date('Y-m-d');
        $filename = "form_{$form_id}_submissions_{$date}.json";
        $file_path = $this->storage_dir . '/' . $filename;
        
        // Read existing data
        $json_data = [];
        if (file_exists($file_path)) {
            $file_contents = file_get_contents($file_path);
            if (!empty($file_contents)) {
                $json_data = json_decode($file_contents, true);
                if (!is_array($json_data)) {
                    $json_data = [];
                }
            }
        }
        
        // Add new entry
        $json_data[] = $data;
        
        // Write to file
        file_put_contents($file_path, json_encode($json_data, JSON_PRETTY_PRINT));
    }
    
    /**
     * Check file size and notify if needed
     */
    private function check_file_size($form_id) {
        $filename = "form_{$form_id}_submissions.txt";
        $file_path = $this->storage_dir . '/' . $filename;
        
        if (file_exists($file_path) && filesize($file_path) > $this->max_file_size) {
            // Send notification email to admin
            $admin_email = get_option('admin_email');
            $subject = 'Gravity Forms Submission Log File Size Alert';
            $message = "The log file for Form ID {$form_id} has exceeded 5MB in size.\n\n";
            $message .= "File path: {$file_path}\n";
            $message .= "Current size: " . round(filesize($file_path) / 1024 / 1024, 2) . "MB\n\n";
            $message .= "You may want to archive or delete this file to prevent it from using too much disk space.";
            
            wp_mail($admin_email, $subject, $message);
        }
    }
    
    /**
     * Update statistics
     */
    private function update_stats($form_id) {
        $today = date('Y-m-d');
        
        // Initialize if needed
        if (!isset($this->stats[$form_id])) {
            $this->stats[$form_id] = [];
        }
        
        if (!isset($this->stats[$form_id][$today])) {
            $this->stats[$form_id][$today] = 0;
        }
        
        // Increment count
        $this->stats[$form_id][$today]++;
        
        // Save stats
        $this->save_stats();
    }
    
    /**
     * Load statistics
     */
    private function load_stats() {
        $stats_file = $this->storage_dir . '/submission_stats.json';
        
        if (file_exists($stats_file)) {
            $json_data = file_get_contents($stats_file);
            $this->stats = json_decode($json_data, true);
            if (!is_array($this->stats)) {
                $this->stats = [];
            }
        }
    }
    
    /**
     * Save statistics
     */
    private function save_stats() {
        $stats_file = $this->storage_dir . '/submission_stats.json';
        file_put_contents($stats_file, json_encode($this->stats, JSON_PRETTY_PRINT));
    }
    
    /**
     * Add dashboard widget
     */
    public function add_dashboard_widget() {
        wp_add_dashboard_widget(
            'gf_submission_stats',
            'Recent Form Submissions',
            [$this, 'dashboard_widget_callback']
        );
    }
    
    /**
     * Dashboard widget callback
     */
    public function dashboard_widget_callback() {
        echo '<div class="submission-stats-widget">';
        
        // Get form data
        $forms = GFAPI::get_forms();
        
        // Show recent submission stats
        echo '<h3>Today\'s Submissions</h3>';
        echo '<table class="widefat">';
        echo '<thead><tr><th>Form</th><th>Submissions</th></tr></thead>';
        echo '<tbody>';
        
        $today = date('Y-m-d');
        $today_total = 0;
        
        foreach ($forms as $form) {
            $form_id = $form['id'];
            $count = isset($this->stats[$form_id][$today]) ? $this->stats[$form_id][$today] : 0;
            $today_total += $count;
            
            echo '<tr>';
            echo '<td>' . esc_html($form['title']) . '</td>';
            echo '<td>' . esc_html($count) . '</td>';
            echo '</tr>';
        }
        
        echo '<tr class="total"><td><strong>Total</strong></td><td><strong>' . esc_html($today_total) . '</strong></td></tr>';
        echo '</tbody></table>';
        
        // Show file sizes
        echo '<h3>Log File Sizes</h3>';
        echo '<table class="widefat">';
        echo '<thead><tr><th>Form</th><th>File Size</th></tr></thead>';
        echo '<tbody>';
        
        foreach ($forms as $form) {
            $form_id = $form['id'];
            $filename = "form_{$form_id}_submissions.txt";
            $file_path = $this->storage_dir . '/' . $filename;
            
            $size = file_exists($file_path) ? round(filesize($file_path) / 1024, 2) . ' KB' : 'N/A';
            
            echo '<tr>';
            echo '<td>' . esc_html($form['title']) . '</td>';
            echo '<td>' . esc_html($size) . '</td>';
            echo '</tr>';
        }
        
        echo '</tbody></table>';
        echo '</div>';
    }
}

// Initialize the class
new GF_Advanced_Data_Logger();
```