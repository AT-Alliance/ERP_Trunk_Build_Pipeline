pipeline {
	agent any
	stages {
		stage('ERP_B-2_PurgeLivrables') {
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
						println "Purge \'$DirToPurge\' success!!"
					} catch (err){
						println "Purge \'$DirToPurge\' failed: ${err}!!"
					}
				}

			}
		}
	}
}
