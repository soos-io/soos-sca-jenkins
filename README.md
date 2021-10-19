# SOOS Integration with Jenkins

The **Jenkinsfile** provided need to be used in your pipeline task, it will install all the dependencies and download and run the python script that analyse your manifiest file. It can be run using either synchronous or asynchronous mode.

## Supported Languages and Package Managers

*	[Node (NPM)](https://www.npmjs.com/)
*	[Python (pypi)](https://pypi.org/)
*	[.NET (NuGet)](https://www.nuget.org/)
*	[Ruby (Ruby Gems)](https://rubygems.org/)
*	[Java (Maven)](https://maven.apache.org/)

Our full list of supported manifest formats can be found [here](https://kb.soos.io/help/soos-languages-supported).

## Need an Account?
**Visit [soos.io](https://app.soos.io/register) to create your trial account.**

## Setup

The python script needs some parameters to operate that you have to set before running the pipeline.

| Parameters | Default | Description |
| --- | --- | --- |
| SOOS_PROJECT_NAME | " "  | REQUIRED. A custom project name that will present itself as a collection of test results within your soos.io dashboard. |
| SOOS_LATEST_REPO | "https://github.com/soos-io/soos-ci-analysis-python/releases/latest/download"   | REQUIRED. Latest python script release repository URI needed to run the analysis |
| SOOS_COMMIT_HASH | " "  | Blank space required if empty. The commit hash value from the SCM System |
| SOOS_BRANCH_NAME | " "  | Blank space required if empty. The name of the branch from the SCM System |
| SOOS_BRANCH_URI | " "  | Blank space required if empty. The URI to the branch from the SCM System |
| SOOS_BUILD_VERSION | " "  | Blank space required if empty. Version of application build artifacts |
| SOOS_BUILD_URI | " "  | Blank space required if empty. URI to CI build info |
| SOOS_INTEGRATION_NAME | "Jenkins"  | REQUIRED. Informs to SOOS from what CI/CD is running the analysis |
| SOOS_MODE | "run_and_wait"  | REQUIRED. Running mode, alternatives: "async_init" - "async_result" |
| SOOS_ON_FAILURE | "fail_the_build"  | REQUIRED. Stop the building in case of failure |
| SOOS_OPERATING_ENVIRONMENT | " "  | Blank space required if empty. System info regarding operating system, etc. |
| SOOS_DIRS_TO_EXCLUDE | " "  | Blank space required if empty. List (comma separated) of directories (relative to ./) to exclude from the search for manifest files. Example - Correct: bin/start/ ... Example - Incorrect: ./bin/start/ ... Example - Incorrect: /bin/start/'|
| SOOS_FILES_TO_EXCLUDE | " "  | Blank space required if empty. List (comma separated) of files (relative to ./) to exclude from the search for manifest files. Example - Correct: bin/start/manifest.txt ... Example - Incorrect: ./bin/start/manifest.txt ... Example - Incorrect: /bin/start/manifest.txt' |
| SOOS_ANALYSIS_RESULT_MAX_WAIT | 300  | REQUIRED. Maximum seconds to wait for Analysis Result before exiting with error. |
| SOOS_ANALYSIS_RESULT_POLLING_INTERVAL | 10  | REQUIRED. Polling interval (in seconds) for analysis result completion (success/failure.). Min 10. |
| SOOS_CHECKOUT_DIR | "./"  | REQUIRED. Directory where python script will search manifiest files.  |
| SOOS_API_BASE_URL | "https://api.soos.io/api/"  | REQUIRED. The API BASE URI provided to you when subscribing to SOOS services. |


Jenkins needs environment variables which are passed as parameters to python script. These environment variables are stored by checking "Environment variables" on Manage Jenkins>Configure System>Global Properties, and they are required for the script to operate.

| Property | Description |
| --- | --- |
| SOOS_CLIENT_ID | Provided to you when subscribing to SOOS services. |
| SOOS_API_KEY | Provided to you when subscribing to SOOS services. |

#### Authorization
The python script will always attempt to load a specific set of parameters from environment variables first; any environment variable values not found will be loaded from script arguments. It’s recommended to use environment variables for `SOOS_CLIENT_ID` and `SOOS_API_KEY` values while using script arguments for the remaining parameters. These values can be found in the SOOS App under Integrate.

### Run and wait for the analysis report
Set the `SOOS_MODE` parameter to *run_and_wait*, then you can run the pipeline in your CI/CD, and wait for the scan to complete.

#### Start the Scan
Set the `SOOS_MODE` parameter to *async_init*, if you don't care about the scan result in your CI/CD pipeline, this is all you have to do!

#### Wait for the Scan
If you care about the result or want to break the build when issues occur, add a second step close to the end of your build pipeline/steps to give the scan as much time as possible to complete, setting the `SOOS_MODE` parameter to *async_result*.

## Feedback and Support
### Knowledge Base
[Go To Knowledge Base](https://kb.soos.io/help)

