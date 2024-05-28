# dbt-ci-cd

dbt-core and GitHub Actions. 

🛠️ Dedicated schema:
- The CI now runs modified models in a schema named pr_<some_number>, e.g., pr_507, which is dropped at the end of each run.

- The schemas have a suffix number to prevent conflicts, allowing multiple developers to test changes simultaneously in different schemas.

🔧 Configuration Updates:
profiles.yml: Added a 'pr' target where the schema is defined as "pr_{{ var('pr_number') }}".

dbt_project.yml: Added an on-run-end hook to drop the schema if the target is 'pr': {% if target.name == 'pr' %} drop schema {{ target.schema }} cascade {% endif %}

ci.yml:
- Added a step to generate a random number for the PR schema.
- Changed dbt command targets from '--target dev' to '--target pr --vars "pr_number: $PR_NUMBER"' in all relevant steps.



🤔 Ok, but how do I run only the modified models if it is a new schema? What about the upstream models?

I added the --defer flag to the build command
dbt build -s 'state:modified+' --defer --state ./ --target pr --vars "pr_number: $PR_NUMBER"

Now the pr schema will refer to the production models to build the modified models we need to test.
