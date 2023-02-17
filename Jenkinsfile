pipeline {
  agent any
  stages {
    stage('CheckoutTrunk') {
      steps {
        svn checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', locations: [[cancelProcessOnExternalsFail: true, credentialsId: '7ef78ec7-cf02-4873-b585-3be09937c0e9', depthOption: 'infinity', ignoreExternalsOption: true, local: '.', remote: 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk']], quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }

    stage('RestoreNuget') {
      parallel {
        stage('RestoreNuget') {
          steps {
            bat '"%WORKSPACE%\\build\\nuget.exe" restore "%WORKSPACE%\\GCRADC.sln"'
          }
        }

        stage('PurgeLivrablesDir') {
          steps {
            powershell 'aaa'
          }
        }

      }
    }

    stage('BuildSolution') {
      steps {
        script {
          try {
            // Build solution step
            powershell 'C:\\\'Program Files (x86)\'\\\'Microsoft Visual Studio\'\\2019\\Professional\\MSBuild\\Current\\Bin\\MSBuild.exe .\\GCRADC.sln /p:Configuration=Release'
            println "Build GCRADC.sln successfull!!"
          } catch (err){
            println "Build GCRADC.sln failed: ${err}"
          }
        }

      }
    }

  }
}