# Scorecard Checks

Jetty Scorecard runs different checks to evaluate a Snowflake configuration. These checks are all instances of the Check class, making it easy to develop and integrate additional tests.

## Existing checks

-   **Network Policy**: Look for an existing network policy
-   **Shadow Future Grants**: Look for ignored db-level future grants
-   **Backup ACCOUNTADMIN**: Make sure that there is more than one user with the ACCOUNTADMIN role
-   **Too many admins**: Make sure a limited number of users have ACCOUNTADMIN or SECURITYADMIN roles
-   **Inactive Users**: Highlight users who haven't logged in or accessed data in a while
-   **Managed Access Schemas**: Look for existing managed access schemas

## Developing a new check

To add a new check, create a function that will return a Check instance, and then then run that function in all_checks.py (you will see where the existing checks are run).

When creating a new Check instance, the majority of the work typically goes into the runner function. This is a function that accepts a Snowflake Environment as a parameter, and then returns a score and a details string. The score represents the numeric result of the check, typically between 1 and 0. Other values can represent special check results:

-   **None**: The check was not run
-   **-2**: The check provides an insight (such as a list of most commonly used tables), but doesn't have a pass/fail score
-   **-1**: The check provides information (such as information about masking policies for non-enterprise accounts), but doesn't have a pass/fail score

For values between 0 and 1, anything at or above 0.9 is a "pass", at or above 0.5 is a "warning", and below 0.5 is a failure.

These scores are used to calculate the final scorecard letter grade.

The details return value is a string that will be inserted into the details section of a collapsible check card. This can include html.

### Using `--load` and `--dump` CLI flags

To test checks without constantly querying your database, you can use the `--load` and `--dump` flags to reuse old environments.