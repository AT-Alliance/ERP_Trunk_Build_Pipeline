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
      environment {
        SourceDir = 'C:\\Jenkins\\JenkinsHome\\workspace\\ERP_Pipeline_master'
        DestinationDir = 'C:\\Livrables'
        BaseOutputDirectory = 'All_dotnet'
      }
      parallel {
        stage('ERP_C-1_BuildSolution') {
          steps {
            script {
              try {
                // Build solution step
                powershell 'C:\\\'Program Files (x86)\'\\\'Microsoft Visual Studio\'\\2019\\Professional\\MSBuild\\Current\\Bin\\MSBuild.exe .\\GCRADC.sln /p:Configuration=Release /p:DeployOnBuild=true'
                println "Build GCRADC.sln successfull!!"
              } catch (err){
                println "Build GCRADC.sln failed: ${err}"
              }
            }

          }
        }

        stage('ERP_C-2_CopyWorkspaceLivrables') {
          steps {
            script {
              try {
                // Get some code from a svn trunk repository
                powershell '''
#$SourceDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_Pipeline_master"
$SourceDirectory="$($env:SourceDir)"
#$DestinationDirectory="C:\\Livrables"
$DestinationDirectory="$($env:DestinationDir)"
#$DestinationDirectoryName="All_dotnet"
$DestinationDirectoryName="$($env:BaseOutputDirectory)"
$count=0

"$SourceDirectory"
"$DestinationDirectory"
"$DestinationDirectoryName"

#Creer le repertoire de base du livrable s\'il n\'existe pas
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName)") -and ($($DestinationDirectoryName) -ne "") ) {

New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName)"
"Le repertoire \'$($DestinationDirectoryName)\' specifie inexistant a ete créé dans \'$($DestinationDirectory)\'"
"-------------------------"
}

if ( (Test-Path $($SourceDirectory)) -and (Test-Path $($DestinationDirectory)) ) {

$DestinationDirectory="$($DestinationDirectory)\\$($DestinationDirectoryName)"
"$DestinationDirectory"
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
#Delete .svn directory
$GetAllDestDirectory=gci  $($DestinationDirectory) -Directory -Recurse
$GetAllDestDirectory |%{

if ( (Test-Path $_.FullName -PathType Container) -and ($_.BaseName -eq '.svn') ) {
Remove-Item $($_.Fullname) -Force -Recurse
"Repertoire '$($_.Fullname)' supprimé"
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
        DestinationDir = 'C:\\Livrables'
        BaseOutputDirectory = 'All_dotnet'
      }
      steps {
        powershell '''$vstestDir="C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Professional\\Common7\\IDE\\Extensions\\TestPlatform"
$listeDLLs=\'Common.DaosTests.dll\',\'Common.ServicesTests.dll\',\'CommonTests.dll\'
$DestinationDirectory="$($env:DestinationDir)"
$DestinationDirectoryName="$($env:BaseOutputDirectory)"

foreach ($it in $listeDLLs) {
	Try {
        if ( $it -eq "Common.DaosTests.dll" ) {
		    $rep="Tests.Daos"
        } elseif ( $it -eq "Common.ServicesTests.dll" ) {
            $rep="Tests.Services"
        } elseif ( $it -eq "CommonTests.dll" ) {
            $rep="Tests.Commun"
        }
        
        . "$($vstestDir)\\vstest.console.exe" "$($DestinationDirectory)\\$($DestinationDirectoryName)\\$($rep)\\$($it)"
        "`nExecution terminée sans erreur pour \'$($rep)\\$($it)\' !!`n"
        "-------------------------`n"
	} catch {
        "`nErreur lors de l\'execution de \'$($rep)\\$($it)\' !!`n"
		"An error occurred: $_"
        "`n-------------------------`n"
	}

}'''
      }
    }

    stage('ERP_E_InstallNpm') {
			environment {
        GenererAngular = 'Oui'
      }
      steps {
        powershell '''# --- DEBUT PORTAGE ------------------------------------------------------------------------------------------------- 

#$BaseOutputRootDirectory="${WORKSPACE}"
$BaseOutputRootDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_Pipeline_master"
#$GenererateAngularOrNot="$($env:GenererAngular)"
$GenererateAngularOrNot="Oui"

Try {

    if ($GenererateAngularOrNot -eq "Oui") {
      Set-Location "$($BaseOutputRootDirectory)\\Portage.Angular\\app\\src"
      . npm install
    }
} catch {
    "An error occurred: $_"
}'''
      }
    }

  }
}
