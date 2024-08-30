# Sonarcube
## Runing analyzer script 
``` cmd
    - Dka.NetCore.Project1
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project1" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password1"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project1"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password1"

    - Dka.NetCore.Project2
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project2" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password2"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project2"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password2"

    - Dka.NetCore.Project3
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project3" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password3"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project3"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password3"

    - Dka.NetCore.Project4
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project4" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password4"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project4"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password4"

    - Dka.NetCore.Project5
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project5" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password5"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project5"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password5"

    - Dka.NetCore.Project6
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project6" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password6"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project6:Rebuild"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password6"

    - Dka.NetCore.Project7
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project7" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password7"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project7:Rebuild"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password7"

    - Dka.NetCore.Project8
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project8" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password8"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project8:Rebuild"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password8"

    - Dka.NetCore.Project9
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project9" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password9"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project9:Rebuild"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password9"

    - Dka.NetCore.Project10
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" begin /k:"Dka.NetCore.Project10" /d:sonar.host.url="http://localhost:9000" /d:sonar.login="Password10"
        - "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\MSBuild.exe" "[path]\Dka.NetCore.Projects.sln" /t:"Dka.NetCore.Project10:Rebuild"
        - "sonar-scanner-msbuild-4.2.0.1214-net46\SonarScanner.MSBuild.exe" end /d:sonar.login="Password10"
```
