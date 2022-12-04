pipeline {
    agent any
    environment {
      branch     = "v0.2-rc"
      new_branch = false
      CRED       = credentials('key-ssh')
    }
    options { 
        timestamps() 
            }
     stages {
        stage('Build') { //----------------
            steps {
                script {
                    if (env.GIT_COMMIT != env.GIT_PREVIOUS_SUCCESSFUL_COMMIT) {
                        
                        /* "----------- количество тэгов  ------------------" */
                        def k= sh returnStdout: true, script: "git tag | wc -l"
                        
                        /* "----------- список тэгов -------------------- " */
                        def output = sh returnStdout: true, script: 'git tag'
                        
                        /* "------ перебор в цикле количество тэгов и увеличение на 0.1 к новому коммиту" */
                        int num = k as Integer     // преообразование в целое число
                        for (int i = 0; i <= num; i++) {
                            if (i==num){
                                /* "----condition last tag v0.${i}, next tag v0.${i+1}" */
                                sh("git tag -a -f v0.${num+1} -m 'Iteration with tag ${num+1}' ")
                                
                                /* "--- New branch is ${branch}${num+1}  --------" */
                                new_branch = sh returnStdout: true, script: "git branch ${branch}${num+1}"
                                t="v0.${num+1}"
                              
                                /* "--- Create new dir and symlink  ------------" */
                                sh """
                                mkdir -p /srv/site/releases/${t}
                                cp index.html /srv/site/releases/${t}
                                ln -sfT /srv/site/releases/${t} /srv/site/releases/current
                                """
                                /* "------------- PUSH ---------" */
                                sshagent( ['key-ssh'] ) {
                                    sh ("git push -f --tags")
                                    sh ("git push --set-upstream origin ${branch}${num+1}")
                                }
                            }
                        }
                    }       
                    else { echo "Новых коммитов нет" }
                }
            }
        }
     }
} // pipline
