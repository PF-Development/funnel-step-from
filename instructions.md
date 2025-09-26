# GA4 Funnel Tracking Implementation Instructions

## Overview
This document provides comprehensive instructions for the Google Analytics 4 (GA4) implementation in the PurpleFire conversion funnel. All code comments and documentation must be maintained in English.

## Implementation Details

### 1. Google Analytics 4 Setup
The GA4 tracking code has been installed in the `<head>` section with tracking ID: `G-DPJF0CSF3B`

```html
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-DPJF0CSF3B"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-DPJF0CSF3B');
</script>
```

### 2. Event Structure and Naming Convention

Simple, focused event tracking for clear funnel analysis:

#### Core Funnel Events
- `funnel_start` - User starts the funnel (page load)
- `step_complete` - When a user completes each step (1-7)
- `step_bounce` - When a user exits without completing a step
- `answers_of_step_X` - Captures user responses when Continue is clicked (where X is step number 1-7)
- `conversion` - Final lead submission success
- `challenge_selected` - Challenge card selection (Step 2)

#### Engagement Events (Optional)
- `engagement` - 30-second interval tracking
- `scroll` - First scroll interaction  
- `user_engagement` - Mouse activity tracking
- `form_interaction` - Form field interactions

### 3. "Answers of Step" Event Details

The `answers_of_step_X` event is crucial for understanding user responses at each funnel step. This event fires ONLY when the user clicks "Continue" or "Submit", ensuring data accuracy by capturing committed responses rather than partial inputs.

#### Event Parameters by Step:
- **Step 1**: `action: 'started_audit'`
- **Step 2**: `challenge_type: 'low-conversion'|'low-aov'|'low-subscription'|'none'`
- **Step 3**: 
  - `monthly_revenue_value: [numeric value]`, `monthly_revenue_status: 'provided'|'empty'`
  - `monthly_ad_spend_value: [numeric value]`, `monthly_ad_spend_status: 'provided'|'empty'`
- **Step 4**: 
  - `conversion_rate_value: [numeric value]`, `conversion_rate_status: 'provided'|'empty'`
  - `aov_value: [numeric value]`, `aov_status: 'provided'|'empty'`
  - `subscription_rate_value: [numeric value]`, `subscription_rate_status: 'provided'|'empty'`
- **Step 5**: `website_url: 'provided'|'empty'`, `page_type: 'Home'|'Landing Page'|'collection'|'product'|'other'|'none'`
- **Step 6**: `action: 'analysis_completed'`
- **Step 7**: `name: 'provided'|'empty'`, `email: 'provided'|'empty'`, `phone: 'provided'|'empty'`

#### Data Quality Benefits:
- **Prevents Invalid Data**: Only tracks when user commits to moving forward
- **Accurate Response Tracking**: Shows actual user selections vs. partial inputs
- **Clean Funnel Analysis**: Each answer represents a genuine user decision
- **Response Pattern Analysis**: Understand which combinations of answers lead to conversions

### 4. Event Parameters

Each event includes comprehensive parameters for detailed analysis:

#### Standard Parameters (included in all events)
- `step_number` - Current funnel step (1-7)
- `funnel_name` - Always "conversion_report_funnel"
- `timestamp` - ISO timestamp
- UTM parameters (source, medium, campaign, term, content)
- `landing_page` - Full landing page URL

#### Step-Specific Parameters
- `step_name` - Human-readable step name
- `step_action` - Action type (view, start, complete, return)
- `progress_percent` - Completion percentage
- `total_steps` - Total funnel steps (7)
- `step_completed` - Boolean completion status

#### Engagement Parameters
- `engagement_time` - Time spent on step
- `engagement_score` - Calculated engagement value
- `scroll_depth` - Scroll percentage
- `mouse_activity` - Mouse movement count
- `time_on_step` - Duration on current step

### 4. Funnel Step Breakdown

#### Step 1: Landing Page
- **Answers Event**: `answers_of_step_1` with `action: 'started_audit'`
- **Completion Event**: `step_complete` with `step_name: 'Website URL Entered'`
- **Bounce Tracking**: Exit before clicking start

#### Step 2: Challenge Selection  
- **Answers Event**: `answers_of_step_2` with `challenge_type` (low-conversion, low-aov, low-subscription)
- **Completion Event**: `step_complete` with `step_name: 'Challenge Selected'`
- **Selection Event**: `challenge_selected` with challenge type
- **Bounce Tracking**: Exit before selecting challenge

#### Step 3: Revenue & Ad Spend Input
- **Answers Event**: `answers_of_step_3` with `monthly_revenue` and `monthly_ad_spend` status
- **Completion Event**: `step_complete` with `step_name: 'Revenue Entered'`
- **Bounce Tracking**: Exit before entering financial data

#### Step 4: Shopify Metrics
- **Answers Event**: `answers_of_step_4` with `conversion_rate`, `aov`, `subscription_rate` status
- **Completion Event**: `step_complete` with `step_name: 'Ad Spend Entered'`
- **Bounce Tracking**: Exit before entering metrics

#### Step 5: Website & Page Type
- **Answers Event**: `answers_of_step_5` with `website_url` and `page_type` 
- **Completion Event**: `step_complete` with `step_name: 'Company Info Completed'`
- **Bounce Tracking**: Exit before completing website info

#### Step 6: Analysis Processing
- **Answers Event**: `answers_of_step_6` with `action: 'analysis_completed'`
- **Completion Event**: `step_complete` with `step_name: 'Analysis Started'`
- **Bounce Tracking**: Exit during analysis wait

#### Step 7: Results & Lead Capture
- **Answers Event**: `answers_of_step_7` with `name`, `email`, `phone` status
- **Results Event**: `step_complete` with `step_name: 'Results Generated'`
- **Final Conversion**: `step_complete` with `step_name: 'Lead Submitted'` + `conversion` event
- **Bounce Tracking**: Exit before submitting lead form

### 5. Bounce Rate Analysis

Simple bounce tracking for each step:

#### Step Bounce Events (`step_bounce`)
- **page_exit**: User closes/leaves page
- **tab_hidden**: User switches tabs  
- **back_button**: User clicks back button
- **timeout**: User inactive (future enhancement)

#### Bounce Rate Calculation
- Step Bounce Rate = `step_bounce` events / (`step_complete` + `step_bounce`) events for each step
- Overall Funnel Drop-off = Compare step completion counts step-by-step
- Time on Step = Tracked with each bounce event for analysis

#### Engagement Metrics (Optional)
- **30-Second Intervals**: Continuous engagement tracking
- **Scroll Tracking**: First scroll interaction detection  
- **Mouse Activity**: Active cursor movement monitoring
- **Form Interactions**: Field focus and input tracking

### 6. GA4 Dashboard Recommendations

#### Key Reports to Create in GA4:

1. **Funnel Analysis Report**
   - Event: `funnel_step_view`
   - Dimension: `step_number`
   - Metric: User count, conversion rate

2. **Step Completion Rates**
   - Event: `funnel_step_complete`
   - Dimension: `step_number`
   - Calculate completion rate between steps

3. **Exit Points Analysis**
   - Event: `funnel_exit`
   - Dimension: `step_number`, `exit_reason`
   - Identify drop-off patterns

4. **Engagement Quality**
   - Event: `engagement`
   - Dimension: `engagement_time`, `step_number`
   - Measure user engagement depth

5. **Form Interaction Analysis**
   - Event: `form_interaction`
   - Dimension: `field_name`, `step_number`
   - Track form completion patterns

#### Custom Conversions to Set Up:
1. **Step 2 Completion** - Challenge selection
2. **Step 5 Completion** - Full form data entry
3. **Final Conversion** - Lead submission
4. **High Engagement** - 2+ minutes on site

### 7. Code Maintenance Guidelines

#### Coding Standards
- All comments must be in English
- Use descriptive variable names in English
- Maintain consistent event naming conventions
- Follow Google Analytics 4 best practices

#### Function Documentation
Each tracking function includes:
- Purpose description in English
- Parameter explanations
- Console logging for debugging
- Error handling for gtag availability

#### Testing and Debugging
- Console logs show all GA4 events with colored output
- Event parameters logged for verification
- Graceful degradation if GA4 not loaded
- UTM parameter capture and logging

### 8. Performance Considerations

#### Optimization Features
- Conditional gtag checks prevent errors
- Throttled engagement tracking (30-second intervals)
- Efficient event parameter reuse
- Minimal performance impact on user experience

#### Data Quality
- Validated form inputs before tracking
- Consistent parameter naming across events
- UTM parameter preservation throughout session
- Timestamp accuracy for proper sequencing

### 9. Privacy and Compliance

#### Data Collection
- No personally identifiable information in standard events
- Form data only tracked as interaction events, not values
- IP anonymization handled by GA4 automatically
- GDPR-compliant implementation

#### User Consent
- Consider implementing consent management if required
- Events fire regardless of consent status
- Data retention follows GA4 default settings

### 10. Future Enhancements

#### Recommended Additions
- Enhanced Ecommerce tracking for calculated conversion values
- Custom dimensions for industry and challenge type
- Audience segmentation based on engagement levels
- A/B testing integration for funnel optimization

#### Monitoring and Maintenance
- Regular review of event firing in GA4 DebugView
- Monthly analysis of funnel performance
- Quarterly review of tracking implementation
- Annual audit of GA4 configuration

---

**Note**: This implementation provides comprehensive tracking for funnel analysis, user behavior understanding, and conversion optimization. All events are designed to give maximum insight into user journey and drop-off points while maintaining performance and user experience.

**Language Policy**: All code comments, variable names, function names, and documentation must remain in English to maintain consistency and international compatibility.