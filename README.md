# SOOS SCA for Jenkins

<img src="assets/SOOS_logo.png" style="margin-bottom: 10px;" width="350" alt="SOOS Icon">

SOOS is the affordable, easy-to-integrate Software Composition Analysis solution for your whole team.

## Features
- Scan your Open Source Software, Webapps and Containers for vulnerabilities
- Ease of integration (run your first scan in minutes or get your first scan results within minutes)
- Control the introduction of new dependencies and vulnerabilities
- Exclude unwanted license-types
- Detect and prevent dependency substitutions and typo-squatting
- Generate SBOMs
- Fill out your compliance worksheets with confidence
- Simple and affordable pricing. Only one plan that includes every feature we offer plus unlimited projects, unlimited users, and no scan limits.

## How to Use

The **Jenkinsfile** provided need to be used in your pipeline task, it will locate and analyze any supported manifest files under the specified directory.

To use this jenkinsfile you need to:

1. [Install docker](#install-docker)
2. [Use the example](#example)
3. [Configure authorization](#configure-authorization)
4. [Select the mode](#select-the-mode)
5. [Configure parameters](#configure-parameters)

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

### Install docker

Need to install docker: [docker.io](https://www.docker.com/)

### Example

<blockquote style="margin-bottom: 10px;">
<details>
<summary> Show example </summary>

``` sh
  pipeline{
      agent {
          docker { 
              image 'python'
              args '-e SOOS_CLIENT_ID=${SOOS_CLIENT_ID} -e SOOS_API_KEY=${SOOS_API_KEY}'
          }
      }
      environment {

          SOOS_PROJECT_NAME=" " // ENTER YOUR PROJECT NAME HERE

          SOOS_LATEST_REPO="https://github.com/soos-io/soos-ci-analysis-python/releases/latest/download"

          SOOS_COMMIT_HASH=" "                // ENTER COMMIT HASH HERE IF KNOWN

          SOOS_BRANCH_NAME=" "                // ENTER BRANCH NAME HERE IF KNOWN

          SOOS_BRANCH_URI=" "                 // ENTER BRANCH URI HERE IF KNOWN

          SOOS_BUILD_VERSION=" "              // ENTER BUILD VERSION HERE IF KNOWN

          SOOS_BUILD_URI=" "                  // ENTER BUILD URI HERE IF KNOWN

          SOOS_OPERATING_ENVIRONMENT=" "      // ENTER OPERATING ENVIRONMENT HERE IF KNOWN (default will be provided)

          SOOS_INTEGRATION_NAME="Jenkins"

          SOOS_MODE="run_and_wait"

          SOOS_ON_FAILURE="fail_the_build"

          SOOS_DIRS_TO_EXCLUDE="soos"

          SOOS_FILES_TO_EXCLUDE=" "

          SOOS_ANALYSIS_RESULT_MAX_WAIT=300

          SOOS_ANALYSIS_RESULT_POLLING_INTERVAL=10

          SOOS_CHECKOUT_DIR="./"

          SOOS_API_BASE_URL="https://api.soos.io/api/"

      }
      stages{
          stage("Test"){
              steps{
                  echo "======== Testing Python Script as RUN_AND_WAIT ========"
          
                  sh '''
                      WORKSPACE=${PWD}

                      echo "creating soos folder"

                      mkdir -p $WORKSPACE/soos/workspace/
                      
                      echo "try to downloading files if they don't exist:"

                      cd $WORKSPACE/soos
                      
                      # if files do not exist, will download them

                      [ -f 'soos.py' ] && 
                      echo "soos.py exists!" ||
                      curl -LJO https://github.com/soos-io/soos-ci-analysis-python/releases/latest/download/soos.py -o soos.py
                      
                      [ -f 'requirements.txt' ] &&
                      echo "requirements.txt exists!" || 
                      curl -LJO https://github.com/soos-io/soos-ci-analysis-python/releases/latest/download/requirements.txt -o requirements.txt
                      
                      echo "files downloaded" 
                      
                      python3 -m venv venv

                      . venv/bin/activate 
                  
                      cd $WORKSPACE

                      pip3 install -r soos/requirements.txt
                      
                      python3 -u soos/soos.py -m=${SOOS_MODE} -of=${SOOS_ON_FAILURE} -dte=${SOOS_DIRS_TO_EXCLUDE} -fte=${SOOS_FILES_TO_EXCLUDE} -wd=${SOOS_CHECKOUT_DIR} -armw=${SOOS_ANALYSIS_RESULT_MAX_WAIT} -arpi=${SOOS_ANALYSIS_RESULT_POLLING_INTERVAL} -buri=${SOOS_API_BASE_URL} -scp=${SOOS_CHECKOUT_DIR} -pn=${SOOS_PROJECT_NAME} -ch=${SOOS_COMMIT_HASH} -bn=${SOOS_BRANCH_NAME} -bruri=${SOOS_BRANCH_URI} -bldver=${SOOS_BUILD_VERSION} -blduri=${SOOS_BUILD_URI} -oe=${SOOS_OPERATING_ENVIRONMENT} -intn=${SOOS_INTEGRATION_NAME}
                  '''
      
              }
              post{
                  success{
                      echo "======== Test executed successfully ========"
                  }
                  failure{
                      echo "======== Test execution failed ========"
                  }
              }
          }
      }
      post{
          always{
              echo "======== Pipeline Finished! ========"
          }
          success{
              echo "======== pipeline executed successfully! ========"
          }
          failure{
              echo "========pipeline execution failed========"
          }
      }
  }
```
</details>
</blockquote>

### Configure authorization

Jenkins needs environment variables which are passed as parameters to python script. These environment variables are stored by checking "Environment variables" on Manage Jenkins>Configure System>Global Properties, and they are required for the script to operate.

| Property | Description |
| --- | --- |
| SOOS_CLIENT_ID | Provided to you when subscribing to SOOS services. |
| SOOS_API_KEY | Provided to you when subscribing to SOOS services. |

The python script will always attempt to load a specific set of parameters from environment variables first; any environment variable values not found will be loaded from script arguments. It’s recommended to use environment variables for `SOOS_CLIENT_ID` and `SOOS_API_KEY` values while using script arguments for the remaining parameters. These values can be found in the SOOS App under Integrate.

### Select the mode

#### Run and wait for the analysis report
Set the `SOOS_MODE` parameter to *run_and_wait*, then you can run the pipeline in your CI/CD, and wait for the scan to complete.

#### Start the Scan
Set the `SOOS_MODE` parameter to *async_init*, if you don't care about the scan result in your CI/CD pipeline, this is all you have to do!

#### Wait for the Scan
If you care about the result or want to break the build when issues occur, add a second step close to the end of your build pipeline/steps to give the scan as much time as possible to complete, setting the `SOOS_MODE` parameter to *async_result*.

### Configure parameters

The python script needs some parameters to operate that you have to set before running the pipeline.

<blockquote style="margin-bottom: 10px;">
<details>
<summary> Show parameters </summary>

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

</details>
</blockquote>

## Feedback and Support
### Knowledge Base
[Go To Knowledge Base](https://kb.soos.io/help)

