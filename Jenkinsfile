pipeline {
  agent any
  stages {
    stage('ERP_A_TrunkCheckout') {
      steps {
        svn checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', locations: [[cancelProcessOnExternalsFail: true, credentialsId: '7ef78ec7-cf02-4873-b585-3be09937c0e9', depthOption: 'infinity', ignoreExternalsOption: true, local: '.', remote: 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk']], quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }

    stage('ParallelStage_1') {
      environment {
        DirToPurge = 'C:\\Livrables\\All_dotnet'
      }
      parallel {
        stage('ERP_B-1_RestoreNuget') {
          steps {
            bat '"%WORKSPACE%\\build\\nuget.exe" restore "%WORKSPACE%\\GCRADC.sln"'
          }
        }

        stage('ERP_B-2_PurgeLivrables') {
          steps {
            script {
              try {
                powershell '''
$DirectoryToPurge="$($env:DirToPurge)"
#$DirectoryToPurge="C:\\Livrables\\All_dotnet"
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
                println "Purge \'$DirToPurge\' success!!"
              } catch (err){
                println "Purge \'$DirToPurge\' failed: ${err}!!"
              }
            }

          }
        }

      }
    }

    stage('ParallelStage_2') {
      parallel {
        stage('ERP_C-1_BuildSolution') {
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

        stage('ERP_C-2_CopyWorkspaceLivrables') {
          environment {
            SourceDir = 'C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main'
            DestinationDir = 'C:\\Livrables'
            BaseOutputDirectory = 'All_dotnet'
          }
          steps {
            script {
              try {
                // Get some code from a svn trunk repository
                powershell '''
#$SourceDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main"
#$SourceDirectory = "\\\\aci-cicd\\Livrables\\All_dotnet\\Tests.*"
$SourceDirectory="$($env:SourceDir)"
#$DestinationDirectory = "\\\\ALLIANCE-VM03\\c$\\Livrables"
$DestinationDirectory="$($env:DestinationDir)"
#$DestinationDirectoryName = "All_dotnet"
$DestinationDirectoryName="$($env:BaseOutputDirectory)"
$count=0
#Creer le repertoire de base du livrable s\'il n\'existe pas
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName)") -and ($($DestinationDirectoryName) -ne "") ) {
New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName)"
"`nLe repertoire \'$($DestinationDirectoryName)\' specifie inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
"-------------------------"
}
if ( (Test-Path $($SourceDirectory)) -and (Test-Path $($DestinationDirectory)) ) {
$DestinationDirectory="$($DestinationDirectory)\\$($DestinationDirectoryName)"
$SourceDirectory |%{
$SourceDirectoryDirs=gci  $($SourceDirectory) -Directory
$SourceDirectoryFiles=gci $($SourceDirectory) -File
#Copy directories from SourceDirectory to DestinationDirectory
$SourceDirectoryDirs |%{
$item=$_
if (Test-Path $($SourceDirectory) -PathType Container) {
Copy-Item -Path "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Recurse -Force
"Repertoire \'$($item.Name)\' copié"
$count++
}
}
"-------------------------"
#Delete .svn directory
$GetAllDestDirectory=gci  $($DestinationDirectory) -Directory -Recurse
$GetAllDestDirectory |%{
if ( (Test-Path $_.FullName -PathType Container) -and ($_.BaseName -eq \'.svn\') ) {
Remove-Item $($_.Fullname) -Recurse -Force
"Repertoire \'$($_.Fullname)\' supprimé"
}
}
"-------------------------"
#Copy files from SourceDirectory to DestinationDirectory
$SourceDirectoryFiles |%{
$item=$_
Copy-Item "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Force
"Fichier \'$($item.Name)\' copié"
$count++
}
}
"---"
"$($count) items copiés dans \'$($DestinationDirectory)\'"
"---"
} else {
"Erreur!!! Verifier l\'existence des repertoires source et destination!!"
}
'''
                println "Copy \'$SourceDir\' to \'$DestinationDir\\$BaseOutputDirectory\' success!!"
              } catch (err) {
                println "Copy \'$SourceDir\' to \'$DestinationDir\\$BaseOutputDirectory\' failed: ${err}!!"
              }
            }

          }
        }

        stage('ERP_C-3_CopyDLLsLivrables') {
          environment {
            SourceDir = '\\\\aci-cicd\\Livrables\\All_dotnet\\Tests.*'
            DestinationDir = '\\\\ALLIANCE-VM03\\c$\\Livrables'
            BaseOutputDirectory = 'All_dotnet'
          }
          steps {
            script {
              try {
                // Get some code from a svn trunk repository
                powershell '''
#$SourceDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main"
#$SourceDirectory = "\\\\aci-cicd\\Livrables\\All_dotnet\\Tests.*"
$SourceDirectory="$($env:SourceDir)"
#$DestinationDirectory = "\\\\ALLIANCE-VM03\\c$\\Livrables"
$DestinationDirectory="$($env:DestinationDir)"
#$DestinationDirectoryName = "All_dotnet"
$DestinationDirectoryName="$($env:BaseOutputDirectory)"
$count=0
#Creer le repertoire de base du livrable s\'il n\'existe pas
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName)") -and ($($DestinationDirectoryName) -ne "") ) {
New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName)"
"`nLe repertoire \'$($DestinationDirectoryName)\' specifie inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
"-------------------------"
}
if ( (Test-Path $($SourceDirectory)) -and (Test-Path $($DestinationDirectory)) ) {
$DestinationDirectory="$($DestinationDirectory)\\$($DestinationDirectoryName)"
$SourceDirectory |%{
$SourceDirectoryDirs=gci  $($SourceDirectory) -Directory
$SourceDirectoryFiles=gci $($SourceDirectory) -File
#Copy directories from SourceDirectory to DestinationDirectory
$SourceDirectoryDirs |%{
$item=$_
if (Test-Path $($SourceDirectory) -PathType Container) {
Copy-Item -Path "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Recurse -Force
"Repertoire \'$($item.Name)\' copié"
$count++
}
}
"-------------------------"
#Delete .svn directory
$GetAllDestDirectory=gci  $($DestinationDirectory) -Directory -Recurse
$GetAllDestDirectory |%{
if ( (Test-Path $_.FullName -PathType Container) -and ($_.BaseName -eq \'.svn\') ) {
Remove-Item $($_.Fullname) -Recurse -Force
"Repertoire \'$($_.Fullname)\' supprimé"
}
}
"-------------------------"
#Copy files from SourceDirectory to DestinationDirectory
$SourceDirectoryFiles |%{
$item=$_
Copy-Item "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Force
"Fichier \'$($item.Name)\' copié"
$count++
}
}
"---"
"$($count) items copiés dans \'$($DestinationDirectory)\'"
"---"
} else {
"Erreur!!! Verifier l\'existence des repertoires source et destination!!"
}
'''
                println "Copy \'$SourceDir\' to \'$DestinationDir\\$BaseOutputDirectory\' success!!"
              } catch (err) {
                println "Copy \'$SourceDir\' to \'$DestinationDir\\$BaseOutputDirectory\' failed: ${err}!!"
              }
            }

          }
        }

      }
    }

    stage('ERP_D_LaunchDLLs') {
      environment {
				DestinationDir = "C:\\Livrables"
				BaseOutputDirectory = "All_dotnet"
			}
			steps {
				script {
					try {
						powershell '''$vstestDir="C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Professional\\Common7\\IDE\\Extensions\\TestPlatform"
$listeDLLs=\'Common.DaosTests.dll\',\'Common.ServicesTests.dll\',\'CommonTests.dll\'
$BaseOutputRootDirectory="C:\\Livrables"
$BaseOutputDirectory="All_dotnet"
#$BaseOutputDirectory=""
foreach ($it in $listeDLLs) {
	Try {
        if ( $it -eq "Common.DaosTests.dll" ) {
		    $rep="Tests.Daos"
        } elseif ( $it -eq "Common.ServicesTests.dll" ) {
            $rep="Tests.Services"
        } elseif ( $it -eq "CommonTests.dll" ) {
            $rep="Tests.Commun"
        }
        
        . "$($vstestDir)\\vstest.console.exe" "$($BaseOutputRootDirectory)\\$($BaseOutputDirectory)\\$($rep)\\$($it)"
        "`nExecution terminée sans erreur pour \'$($rep)\\$($it)\' !!`n"
        "-------------------------`n"
	} catch {
        "`nErreur lors de l\'execution de \'$($rep)\\$($it)\' !!`n"
		"An error occurred: $_"
        "`n-------------------------`n"
	}
}'''
            println "Launch DLLs in \'$DestinationDir\\$BaseOutputDirectory\' success!!"
					} catch (err){
            println "Launch DLLs in \'$DestinationDir\\$BaseOutputDirectory\' failed: ${err}!!"
					}
				}

			}
    }

  }
}
