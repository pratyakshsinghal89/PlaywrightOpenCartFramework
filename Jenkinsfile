pipeline {
agent any
environment {
VENV_DIR = 'venv'
REPORT_DIR = 'reports'

}
stages {
// Step 1: Checkout Code from GitHub
stage('Checkout Code') {
steps {
git 'https://github.com/pratyakshsinghal89/PlaywrightOpenCartFramework.git'
echo "Code checked out successfully."
}
}
// Step 2: Setup Python Virtual Environment
stage('Setup Python Environment') {
steps {
script {
if (!fileExists("${VENV_DIR}\\Scripts\\activate")) {
echo "Creating virtual environment..."
bat "python -m venv ${VENV_DIR}"
} else {
echo "Virtual environment already exists."
}
}
}
}
// Step 3: Install Required Packages
stage('Install Dependencies') {
steps {
bat """
call ${VENV_DIR}\\Scripts\\activate
python -m pip install --upgrade pip
pip install -r requirements.txt
playwright install
"""
}
}
// Step 4: Clean Old Test Reports
stage('Clean Old Reports') {
steps {
bat """
if exist ${REPORT_DIR} (
rmdir /s /q ${REPORT_DIR}
)
"""
}
}
// Step 5: Run Pytest Tests and Generate Allure Results
stage('Run Tests') {
steps {
bat """
call ${VENV_DIR}\\Scripts\\activate
pytest tests --browser "%browser%" -m "%testgroups%" --alluredir=${REPORT_DIR}\\allure-results
"""
}
}
// Step 6: Publish Allure Report in Jenkins
stage('Publish Allure Report') {
steps {
allure includeProperties: false, jdk: '', results: [[path: "${REPORT_DIR}\\allure-results"]]
}
}
}
post {
always {
echo "Cleaning up workspace after build..."
}
success {
echo "Tests executed successfully!"
}
failure {
echo "Test execution failed!"
}
}
}