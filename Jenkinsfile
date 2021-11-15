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

        SOOS_API_BASE_URL="https://dev-api.soos.io/api/"

    }
    stages{
        stage("Test"){
            steps{
                echo "======== Testing Python Script as RUN_AND_WAIT ========"
        
                sh '''
                    WORKSPACE=${PWD}

                    echo "creating soos folder"

                    mkdir -p "$WORKSPACE/soos/workspace/"
                    
                    echo "try to downloading files if they don't exist:"

                    cd "$WORKSPACE/soos"
                    
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
                 
                    cd "$WORKSPACE"

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