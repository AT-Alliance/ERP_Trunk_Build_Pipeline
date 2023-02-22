pipeline {
  agent any
  stages {
    stage('ERP_A_TrunkCheckout') {
      steps {
        svn checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', locations: [[cancelProcessOnExternalsFail: true, credentialsId: '7ef78ec7-cf02-4873-b585-3be09937c0e9', depthOption: 'infinity', ignoreExternalsOption: true, local: '.', remote: 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk']], quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }

  }
}