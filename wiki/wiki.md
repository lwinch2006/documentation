# Wiki
## Git workflow
```
  - Setting up
    - git config --global user.name "John Doe"
    - git config --global user.email johndoe@example.com
    - git config --global core.editor "'C:/Program Files (x86)/Notepad++/notepad++.exe' -multiInst -nosession"
    - git config --global core.editor "'C:/Program Files (x86)/Geany/bin/geany.exe' --new-instance"
    - git config --global mergetool.codecompare.cmd "'C:\Program Files\Devart\Code Compare\codemerge.exe' -MF \"$LOCAL\" -TF \"$REMOTE\" -BF \"$BASE\" -RF \"$MERGED\""
    - git config --global difftool.codecompare.cmd "'C:\Program Files\Devart\Code Compare\codecompare.exe' -W \"$LOCAL\" \"$REMOTE\""    
    - git config --global branch.autosetuprebase always
    - git config --global user.signingkey 0A46826A
    - git config --global color.status auto
    - git config --global color.branch auto
    - git config --global color.interactive auto
    - git config --global color.diff auto
    - git config --global commit.gpgsign true
    - git config --global gpg.program "c:/Program Files (x86)/GNU/GnuPG/gpg2.exe"
        
  - Initial
    - git branch development                (creating "development" branch)
  - Regular
    - git checkout development              (switching to "development" branch)
    - git rebase master                     (sync "development" branch with "master", resolve conflicts)
    - working on "development" branch
    - git commit -a -S                      (commiting changes done on "development" branch) with signing by OpenPGP card
    - git checkout master                   (switching to "master" branch)
    - git fetch origin                      (sync "master" branch with "origin/master")
    - git merge FETCH_HEAD                  (sync "master" branch with "origin/master")
    - git checkout development              (switching to "development" branch)
    - git rebase master                     (sync "development" branch with "master", resolve conflicts)
    - git checkout master                   (switching to "master" branch)
    - git merge development                 (sync "master" branch with "development")
    - git push origin master                (sync "origin/master" branch with "master")
  - Committing your work to Git
    - git commit -a -S -m 'signed commit' 
    - git log --show-signature
  - Reverting your work in Git
    - working files 
      - git checkout -- .
    - staged files
      - git reset HEAD .
    - untracked files
      - git clean -n -d (show what will be deleted)
      - git clean -f -d (actually delete items: files & directories)
  - Change the most recent comment in commit.
    - git commit --amend -m "New commit message"
    - git push -f (if commit was pushed) 
  - Show log
    - git log
  - Show log with changes
    - git log -p
  - Show log with just file names changed
    - git log --name-only     
```

## Git commands
```
  - List all branches (local)
    git branch
  
  - List all branches (local and remote)
    git branch -a
    
  - List all branches with associations to remote branches
    git branch -vv  

  - Initialize new local repository
    git init

  - Fetch remote changes
    git pull
  
  - Fetch remote changes
    git fetch origin
    git merge FETCH_HEAD
    
  - Fetch remote changes with rebasing
    git pull --rebase   

  - Checkout remote branch
    git checkout [branch]

  - Checkout remote branch and  create new branch from it
    git checkout [old branch] -b [new branch]

  - Stage changes
    git add .
    
  - Unstage changes
    git reset 

  - Commit changes with signature
    git commit -a -S

  - Commit changes and preserve hash symbol "#" in commit message
    git commit -a -S --cleanup=whitespace
    
  - Push changes
    git push
  
  - Push changes for local branch that has no remote branch (no tracking information)
    git push origin [branch name]    
    
  - Push changes for local branch that has no remote branch (with tracking information)
    git push -u origin [branch name]  
    
  - Revert not committed changes
    git checkout -- . / git checkout HEAD .    
    git reset --hard / git reset --hard HEAD
    
  - Unstage changes
    git checkout -- . / git checkout HEAD . (discard changes)
    git reset --hard / git reset --hard HEAD . (discard changes)
    git reset (preserve changes) 
    
  - Revert committed changes
    - git checkout [commit hash] [.|file name] (produce changes to be committed)
    - git revert [commit hash] (produces new commit that reverts another)
    - git reset [commit hash] (reset commit to local changes with other local changes to be committed)
    - git reset --keep [commit hash] (reset commit but preserve local changes)
    - git reset --hard [commit hash] (reset commit and discard local changes)
    - git reset --hard origin/[branch name] (reset local committed changes and sync local branch with the remote)
    
  - Revert pushed changes
  
  - Revert committed merge changes
    git revert -m [1|2] [commit hash]    
    
  - Remove local branch  
    git branch -d [branch]
    
  - Remove remote branch
    git push origin --delete [branch]  
    
  - Remove non-existent remote branch references in your local repository
    git remote prune origin  
```

## Notepad++ environment variables
```
  - FULL_CURRENT_PATH - is the full path to file - C:\Documents\MyFiles\sample.cs
  - CURRENT_DIRECTORY - just the directory where the file is located - C:\Documents\MyFiles
  - FILE_NAME - the file name only - sample.cs
  - NAME_PART - the part that only shows the name of the file without the extension - sample
  - EXT_PART - the file extension - cs
```

## Notepad++ run command
```
  - csc.exe /out:$(CURRENT_DIRECTORY)\$(NAME_PART).exe $(FULL_CURRENT_PATH)
```

# OpenSSL commands
```
  - List properties of a X509 certificate
    openssl x509 -in [certificate.crt] -text -noout

  - List properties of a PKCS12 (PFX) certificate
    openssl pkcs12 -info -in keyStore.p12 

  - Generate self signed certificate
    openssl req -new -sha256 -nodes -out [serverSigningRequest.csr] -newkey rsa:2048 -keyout [serverKey.key]
    openssl x509 -req -in [serverSigningRequest.csr] -CA [rootCACertificate.pem] -CAkey [rootCAKey.pem] -CAcreateserial -out [serverCertificate.crt] -days 365 -sha256 -extfile v3.ext

  - Combine private key and certificate to PKCS12 (PFX)
    openssl pkcs12 -export -out [filename.pfx] -inkey [privateKey.key] -in [certificate.crt]

  - Extract private key from PKCS12 (PFX)
    openssl pkcs12 -in [file.pfx] -nocerts -out [privateKey.pem]

  - Extract certificate from PKCS12 (PFX)
    openssl pkcs12 -in [file.pfx] -clcerts -nokeys -out [publicCert.pem] 
```

## Task Template 1
```csharp
    Task "[Name]"
         "[Name] - XML documentation"
         "[Name] - Manual refactoring"
         "[Name] - Auto refactoring"
         "[Name] - Line reordering"
         "[Name] - Fixing SonarQube cases"
         "[Name] - Fixing TFS issue"
         "[Name] - Making tests"

    #region Steps
    
      - confirm
      - debug
      - fix/develop
      - test
      - review code
      - commit code
      - task done/closed    

    #endregion
    
    #region Development
    
        - branch "[Name]" inherited from "master"
        - branch "[Name]" inherited from "[Name]"
        - branch "[Name]" inherited from "[Name]"
        - branch "[Name]" inherited from "[Name]"
        - branch "[Name]" inherited from "[Name]"
        - branch "[Name]" inherited from "[Name]"                
        - branch "[Name]" inherited from "[Name]"
        - branch "[Name]" inherited from "[Name]"                
        - PR(s) created
        - PR(s) completed/abandoned
    
    #endregion

    #region Testing

    #endregion

    #region Translations

    #endregion
    
    #region Code review

    #endregion    

    #region Comments
        * `FYI`. Please dont merge branch. Just check. I'll merge it myself.
    #endregion    
```

## Task Template 2
``` csharp
    Task "[Name]"

    #region Steps
    
      - confirm
      - debug
      - fix/develop
      - test
      - review code
      - commit code
      - task done/closed    

    #endregion
    
    #region Development
    
        - branch "[Name]" inherited from "master"
        - PR(s) created
        - PR(s) completed/abandoned
    
        - XML documentation
        - Manual refactoring
        - Auto refactoring
        - Lines reordering
        - Fixing SonarQube cases
        - Fixing TFS issue
        - Making tests
        
    #endregion

    #region Testing

    #endregion

    #region Translations

    #endregion
    
    #region Code review

    #endregion    

    #region Comments
    
        * `FYI`. Please check separate commits (e.g. different steps) to see changes easier.
        * `FYI`. Please dont merge branch. Just check. I'll merge it myself.

    #endregion
```

## Visual Studio workflow
```
    - Go to definition
        - "<Alt + |>"
        
    - Clean solution
        - "<Alt + 1>"
        
    - Restore NuGet packages
        - "<Alt + 2>"
        
    - Build solution
        - "<Alt + 3>"
        
    - Rebuild solution
        - "<Alt + 4>"
```

## When calculating checksums in Windows
```
  - Example:
    - c:\Windows\System32\certutil.exe -hashfile "Downloads\dotnet-dev-win-x64.latest.exe" SHA512
```

## When setting up new PC
```
  - Software
    - Windows 10 LTSB
      - disable automatic snap assist
      - add Norwegian and Russian Languages
    - Drivers from Dell
    - Keepass
    - Visual Studio 2017
    - dotnet cli
      - envvar "DOTNET_CLI_TELEMETRY_OPTOUT" = 1
    - Visual Studio Code
    - .NET 4.6.2 framework
    - SQL Server (vNext maybe)
    - SQL Server Management Studio
    - Firefox Nightly
    - Firefox Desktop
    - Firefox Developer Edition
    - Google Chrome
    - Opera
    - Brave browser
    - MS Office
    - MS Outlook
    - Skype
    - Slack
    - Notepad++
    - Code Comparer
    - Pencil
    - Tagspaces
    - ResourceClient
    - GIMP
    - 7z
    - OpenSSL
      - envvar "PATH" updated
    - GPG
      - envvar "PATH" updated
    - ARR
    - URL Rewrite
    - GhostDoc
    - StyleCop
    - GAX
    - LASG
    - Git
```














