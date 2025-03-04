# Gravity Forms Training Plan (1.5 Hours)

## Session Outline (2:00 PM - 3:30 PM)

### Part 1: Gravity Forms Basics (40 minutes)
- Installation and form creation
- Working with fields
- Form settings and submissions

### Part 2: Gravity Forms Hooks (50 minutes)
- Understanding the hook system
- Implementing a custom hook
- Saving form submissions to a file

## Detailed Schedule

### 2:00 - 2:10 PM: Setup & Installation
1. Log into WordPress admin panel
2. Navigate to Plugins > Add New
3. Search for "Gravity Forms" (or upload the plugin if you have the premium version)
4. Install and activate the plugin
5. Navigate to Forms > Settings to verify license (if using premium version)

### 2:10 - 2:30 PM: Create Your First Form
1. Go to Forms > New Form
2. Name the form "Customer Inquiry"
3. Add the following fields exactly:
   - Name (First & Last) - make this required
   - Email - make this required with email validation
   - Phone - make this optional
   - Dropdown titled "Inquiry Type" with these options:
     * General Question
     * Technical Support
     * Billing Issue
     * Feature Request
   - Paragraph Text field titled "Message" - make this required
   - Hidden field named "source" with default value "website"

4. Click "Update" to save the form

### 2:30 - 2:40 PM: Form Settings
1. Navigate to Settings tab of your form
2. Configure Form Settings:
   - Set confirmation message to: "Thank you for your inquiry. We'll get back to you within 24 hours."
   - Create an email notification to admin (your email)
   - Title it "New Customer Inquiry"
   - Set the email to include all fields

3. Test the form by previewing and submitting a test entry

### 2:40 - 2:50 PM: Understanding Hooks
1. Open your theme's functions.php file (or better, create a simple plugin file)
2. Review the WordPress hooks system basics:
   - Actions vs Filters
   - How hooks execute during the form lifecycle
   - Key Gravity Forms hooks available

### 2:50 - 3:20 PM: Implementing a Custom Hook
1. Create a new file called `gravity-form-hooks.php` in the wp-content directory
2. Add this basic plugin header:
```php
<?php
/**
 * Plugin Name: Gravity Forms Custom Hooks
 * Description: Custom hooks for Gravity Forms
 * Version: 1.0
 */

// Make sure direct file access is blocked
if (!defined('ABSPATH')) {
    exit;
}
```

3. Add code to save form submissions to a file:
```php
// Hook into form submission
add_action('gform_after_submission_1', 'save_form_submission_to_file', 10, 2);

function save_form_submission_to_file($entry, $form) {
    // Format the data
    $timestamp = date('Y-m-d H:i:s');
    $form_data = "Submission Time: $timestamp\n";
    $form_data .= "Form ID: {$form['id']}\n";
    $form_data .= "Entry ID: {$entry['id']}\n\n";
    
    // Loop through form fields and add their values
    foreach ($form['fields'] as $field) {
        $field_id = $field->id;
        $field_label = $field->label;
        $field_value = rgar($entry, $field_id);
        
        if (!empty($field_value)) {
            $form_data .= "$field_label: $field_value\n";
        }
    }
    $form_data .= "---------------------\n\n";
    
    // Define file path in wp-content directory
    $file_path = WP_CONTENT_DIR . '/form-submissions.txt';
    
    // Append data to file
    file_put_contents($file_path, $form_data, FILE_APPEND);
}
```

4. Activate the plugin
5. Submit another test entry to the form
6. Check the wp-content directory for the form-submissions.txt file

### 3:20 - 3:30 PM: Review and Additional Hooks

Review some other useful Gravity Forms hooks:
- `gform_pre_render` - Modify form before it's displayed
- `gform_field_validation` - Custom field validation
- `gform_entry_created` - Triggered when an entry is created
- `gform_notification` - Modify notifications

Discuss how hooks can be used for:
- Custom validation
- Calculations
- Integration with other systems
- Conditional routing

## Additional Notes
- The form ID in the hook (`gform_after_submission_1`) may need to be adjusted to match your actual form ID
- For security, ensure the file has proper permissions
- In a production environment, consider more robust storage solutions