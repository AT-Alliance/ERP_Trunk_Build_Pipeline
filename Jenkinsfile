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
													
						$SourceDirectory |% {
														
							$SourceDirectoryDirs=gci  $($SourceDirectory) -Directory
							$SourceDirectoryFiles=gci $($SourceDirectory) -File
														
							$SourceDirectoryDirs |%{
								$item=$_
								if (Test-Path $($SourceDirectory) -PathType Container) { 
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
					}
				'''
				println 'Copy "$($env:SourceDir)" to "$($env:DestinationDir)" success!!'
			} catch (err) {
				println 'Copy "$($env:SourceDir)" to "$($env:DestinationDir)" failed: ${err}!!'
			}
		}
	}
}
