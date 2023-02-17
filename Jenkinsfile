pipeline {
  agent any
  stages {
    stage('CheckoutTrunk') {
      steps {
        svn checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', locations: [[cancelProcessOnExternalsFail: true, credentialsId: '7ef78ec7-cf02-4873-b585-3be09937c0e9', depthOption: 'infinity', ignoreExternalsOption: true, local: '.', remote: 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk']], quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }

    stage('ParallelStages') {
      parallel {
        
        stage('PurgeLivrablesDir') {
				steps {
					script {
						try {
							powershell '''
								#$DirectoryToPurge="$($env:DirToPurge)"
								$DirectoryToPurge="C:\\Livrables\\All_dotnet"
								$count=0
								
								#Creer le repertoire de base du livrable s\\\'il n\\\'existe pas
								if ( Test-Path $($DirectoryToPurge) ) {
									$getAllFilesLivrableDirectory=gci $DirectoryToPurge -File -Recurse
									$getAllFilesLivrableDirectory |%{
										Remove-Item $($_.Fullname) -Recurse -Force
										"Fichier \'$($_.Fullname)\' supprimé"
										$count++
									}
									"---"
									"$($count) fichiers purgés dans \'$($DirectoryToPurge)\'"
									"---"
								} 
							'''
							println 'Purge "$($env:DirToPurge)" success!!'
						} catch (err){
        			    println 'Purge "$($env:DirToPurge)" failed: ${err}!!'
        			}
                }
            }
        }
				
				stage('RestoreNuget') {
          steps {
            bat '"%WORKSPACE%\\build\\nuget.exe" restore "%WORKSPACE%\\GCRADC.sln"'
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
