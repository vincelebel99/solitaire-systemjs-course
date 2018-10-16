stage 'CI'
node {
    
  
  nodejs('NodeJS') {
 

	checkout scm

   // git branch: 'jenkins2-course', 
     //   url: 'https://github.com/vincelebel99/solitaire-systemjs-course'

    // pull dependencies from npm
    // on windows use: bat 'npm install'
    sh '/Users/Vince/.nvm/versions/node/v7.2.0/bin/npm install'

    // stash code & dependencies to expedite subsequent testing
    // and ensure same code & dependencies are used throughout the pipeline
    // stash is a temporary archive
    stash name: 'everything', 
          excludes: 'test-results/**', 
          includes: '**'
    
    // test with PhantomJS for "fast" "generic" results
    // on windows use: bat 'npm run test-single-run -- --browsers PhantomJS'
    sh '/Users/Vince/.nvm/versions/node/v7.2.0/bin/npm run test-single-run -- --browsers PhantomJS'
  } 
    // archive karma test results (karma is configured to export junit xml files)
    step([$class: 'JUnitResultArchiver', 
          testResults: 'test-results/**/test-results.xml'])
          
          
node('mac') {
    
    
    
    sh 'ls'
    sh 'rm -rf *'
    unstash 'everything'
    sh 'ls'
}

          
}

// parallel integration testing
stage 'Browser testing'
parallel chrome: {
    runTests("Chrome")
}, safari: {
    runTests("Chrome")
}



def runTests(browser){
    node {
        
         nodejs('NodeJS') {
        sh 'rm -rf *'
        unstash 'everything'
        sh "/Users/Vince/.nvm/versions/node/v7.2.0/bin/npm run test-single-run -- --browsers ${browser}"
        step([$class: 'JUnitResultArchiver', 
          testResults: 'test-results/**/test-results.xml'])
    }
    }
    
    
}

node {

notify('Deploy to staging?')
}

input 'Deploy to staging ?'

//only one pipeline can deploy on the time

stage name : 'Deploy', concurrency: 1

node {

//write build number to index page so we can see this update
sh "echo '<h1>${env.BUILD_DISPLAY_NAME}</h1>' >> app/index.html"

//deploy to a docker container mapped to port 3000

//sh 'docker-compose up -d --build'

sh 'echo "deployed"'

notify 'Solitaire Deployed'

    
}




def notify(status){
    emailext (
      to: "vlebel99@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}
