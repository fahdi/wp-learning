# Day 2  

## 9:00 AM - 12:00 PM: Practical Advanced Form Features

## 9:00-9:45 AM: Multi-Step Form Implementation

### Initial Setup (15 min)
1. Create a new form named "Multi-Step Service Request"
2. Navigate to Form Settings > "Form Layout" 
3. Enable "Progress Indicator" and select "Progress Bar" style
4. Discuss different progress indicator options and their impact on completion rates

### Page Configuration (30 min)
1. Create Page 1: Contact Information
   - Name (First & Last) field with proper formatting
   - Email field with confirmation enabled
   - Phone field with format validation
   - Company name and website fields
   - Add proper field descriptions and placeholder text

2. Create Page 2: Service Requirements
   - Service Type selection with detailed options
   - Project Timeline dropdown with appropriate ranges
   - Budget Range selection with clear options
   - Project Goals (checkbox field with multiple selections)
   - Team Size and Timeline constraints

3. Create Page 3: Additional Information
   - Project Description (Paragraph Text) with character counter
   - Reference websites/examples field
   - File Upload field for requirements documents
   - Preferred Contact Method and Times
   - Additional Notes section

4. Create Page 4: Review & Submit
   - Summary of selections using HTML field and merge tags
   - Final confirmation checkbox
   - Submit button with custom text

## 9:45-10:45 AM: Implementing Conditional Logic

### Basic Conditional Logic (20 min)
1. Add qualifying questions that determine form flow:
   - "Do you need a custom design?" (Yes/No)
   - "Is this a new project or enhancement?" (New/Enhancement)
   - "Do you have existing brand guidelines?" (Yes/No)

2. Create dependent fields for each scenario:
   - Design requirements fields when custom design is needed
   - Existing system details when enhancement is selected
   - Brand guideline upload when guidelines exist

### Advanced Conditional Scenarios (40 min)
1. Implement conditional page navigation:
   - Configure page breaks to skip irrelevant sections
   - Add custom navigation based on service selection
   - Create shortcuts for returning customers

2. Build multi-condition logic:
   - Create fields that appear only when multiple conditions are met
   - Technical requirements section that shows based on service type AND budget
   - Special offer fields that appear based on budget AND timeline

3. Develop nested conditional scenarios:
   - E-commerce specific fields that trigger additional sub-options
   - Technology stack selection that changes available feature options
   - Integration requirements that affect pricing and timeline fields

## 10:45-11:15 AM: Break & Questions

## 11:15 AM - 12:00 PM: Dynamic Calculations & Specialized Features

### Product Fields & Pricing (25 min)
1. Set up base service product fields with conditional pricing:
   - Website Development ($5,000-$15,000 based on complexity)
   - Mobile App Development ($10,000-$25,000)
   - E-commerce Solution ($7,500-$20,000)
   - Maintenance & Support (variable pricing)

2. Add option fields as add-ons:
   - Rush delivery surcharge
   - SEO package
   - Content creation services
   - Extended support plans
   - Custom feature development

3. Implement quantity fields:
   - Number of pages or screens
   - Number of products for e-commerce
   - Number of revisions included

### Price Calculation Logic (20 min)
1. Set up dynamic total calculation:
   - Configure Product field calculations
   - Add formula fields for complex pricing
   - Create conditional pricing based on volume

2. Add discount functionality:
   - Promo code field with validation
   - Early payment discount options
   - Custom discount field for sales representatives
   - Bundle discount for multiple services

### Advanced File Handling and Validation (25 min)
1. Configure multiple file upload fields:
   - Set allowed file extensions for different purposes
   - Configure maximum file sizes
   - Enable multiple file upload functionality
   - Create appropriate error messages

2. Implement custom field validation:
   - Email domain validation for business accounts
   - Budget/timeline consistency validation
   - Project requirements minimum length
   - Custom phone number formatting

3. Enhance form security:
   - Enable honeypot field
   - Configure CAPTCHA options
   - Set up IP address limiting
   - Implement submission time tracking

## 12:00-12:30 PM: Form Styling & User Experience Enhancements

### Layout Optimization (15 min)
1. Apply CSS Ready Classes:
   - Two-column layout for contact information
   - Multi-column option lists
   - Responsive field arrangements

2. Create visual section dividers:
   - HTML fields with styled headings
   - Section descriptions and instructions
   - Visual progress elements

### User Experience Improvements (15 min)
1. Configure Save & Continue:
   - Enable resume link emails
   - Set appropriate link expiration
   - Create custom save messages

2. Enhance form navigation:
   - Customize button labels based on context
   - Add inline help text
   - Create tooltips for complex options

## 12:30-1:15 PM: Testing & Troubleshooting Advanced Forms

### Form Testing Methodology (20 min)
1. Browser compatibility testing
2. Mobile device testing
3. Conditional logic verification
4. Calculation testing with edge cases

### Common Issues & Solutions (25 min)
1. Troubleshooting conditional logic problems
2. Fixing calculation errors
3. Resolving validation conflicts
4. Debugging JavaScript customizations

This expanded schedule provides more time for each component, allowing your engineer to work through the implementations thoughtfully and understand how each feature works. The added troubleshooting section will help them address common issues that arise with complex forms.