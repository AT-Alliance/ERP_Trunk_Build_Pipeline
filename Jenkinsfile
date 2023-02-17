pipeline {
  agent any
  stages {
    stage('CheckoutTrunk') {
      steps {
        svn checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', locations: [[cancelProcessOnExternalsFail: true, credentialsId: '7ef78ec7-cf02-4873-b585-3be09937c0e9', depthOption: 'infinity', ignoreExternalsOption: true, local: '.', remote: 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk']], quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }

    stage('ParallelStage_1') {
      environment {
        DirToPurge = 'C:\\Livrables\\All_dotnet'
      }
      parallel {
        stage('PurgeLivrablesDir') {
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

    stage('ParallelStage_2') {
      parallel {
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

        stage('CopyDirsFiles') { 
	steps {
		script {
			try {
				// Get some code from a svn trunk repository
				powershell '''
$SourceDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_Pipeline_master"
#$SourceDirectory="$($env:SourceDir)"
$DestinationDirectory="C:\\Livrables"
#$DestinationDirectory="$($env:DestinationDir)"
$DestinationDirectoryName="All_dotnet"
#$DestinationDirectoryName="$($env:BaseOutputDirectory)"
$count=0

#Creer le repertoire de base du livrable s\'il n\'existe pas
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName)") -and ($($DestinationDirectoryName) -ne "") ) {
    
    New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName)"
    "Le repertoire \'$($DestinationDirectoryName)\' specifie inexistant a ete créé dans \'$($DestinationDirectory)\'"
    $DestinationDirectory="$($DestinationDirectory)\\$($DestinationDirectoryName)"
    "-------------------------"
}

if ( (Test-Path $($SourceDirectory)) -and (Test-Path $($DestinationDirectory)) ) {
								
	$SourceDirectory |%{
									
		$SourceDirectoryDirs=gci  $($SourceDirectory) -Directory
		$SourceDirectoryFiles=gci $($SourceDirectory) -File
									
		$SourceDirectoryDirs |%{
			$item=$_

			if (Test-Path $($SourceDirectory) -PathType Container) { #Test-Path -Path $($item) -PathType Container) {
				Copy-Item -Path "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Recurse -Force
                "Repertoire \'$($item.Name)\' copié"
				$count++
			}
		}
		"-------------------------"
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
}'''
				println 'Copy "$($env:SourceDir)" to "$($env:DestinationDir)" success!!'
			} catch (err) {
				println 'Copy "$($env:SourceDir)" to "$($env:DestinationDir)" failed: ${err}!!'
			}
		}
	}
}

      }
    }

  }
}
