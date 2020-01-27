# launchSettings.json
Date: 27.01.2020

```json
{
  "iisSettings": IIS_and_IIS_Express_setting
  {
    "windowsAuthentication": false, Set_to_true_to_enable_windows_authentication_for_your_site_in_IIS_and_IIS_Express.
    "anonymousAuthentication": true, Set_to_true_to_enable_anonymous_authentication_for_your_site_in_IIS_and_IIS_Express.
    "iisExpress": Site_settings_to_use_with_IISExpress_profiles.
    {
      "applicationUrl": "http://localhost:62798/", The_URL_of_the_web_site.
      "sslPort": 62799 The_SSL_Port_to_use_for_the_web_site.
    }
  },
  "iis": Site_settings_to_use_with_IIS_profiles.
  {
      "applicationUrl": "http://localhost:62798/", The_URL_of_the_web_site.
      "sslPort": 62799 The_SSL_Port_to_use_for_the_web_site.  
  },
  "profiles": A_list_of_debug_profiles
  {
    "IIS Express": Name_of_profile
    {
      "commandName": "IISExpress", Identifies_the_debug_target_to_run._Values_can_be_IISExpress_or_IIS_or_Project
      "commandLineArgs": "", The_arguments_to_pass_to_the_target_being_run.
      "executablePath": "", An_absolute_or_relative_path_to_the_to_the_executable.
      "workingDirectory": "", Sets_the_working_directory_of_the_command.
      "launchBrowser": false, Set_to_true_if_the_browser_should_be_launched.
      "launchUrl": "api-docs", The_relative_URL_to_launch_in_the_browser.
      "environmentVariables": Set_the_environment_variables_as_key/value_pairs.
      {
        "ASPNETCORE_ENVIRONMENT": "Development" Environment_variable_name_-_value
      },
      "applicationUrl": "http://localhost:62798;https://localhost:62799", A_semi-colon_delimited_list_of_URL(s)_to_configure_for_the_web_server.
      "nativeDebugging": false, Set_to_true_to_enable_native_code_debugging.
      "externalUrlConfiguration": false, Set_to_true_to_disable_configuration_of_the_site_when_running_the_Asp.Net_Core_Project_profile.
      "use64Bit": false Set_to_true_to_run_the_x64_version_of_IIS_Express,_false_to_run_the_x86_version.
    },
    "Project 1": Name_of_profile
    {
      "commandName": "Project",
      "launchBrowser": true,
      "launchUrl": "api-docs",
      "environmentVariables": 
      {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:62798;https://localhost:62799",
      "sslPort": 62799
    }
  }
}
```

## AspNetCoreHostingModel setting in csproj file
![hosting table of web application depending on CommandName and AspNetCoreHostingModel](images/test.png)

## Literature:
1. [Scheme](http://json.schemastore.org/launchsettings)
2. [ASP.NET Core launchSettings.json file](https://dotnettutorials.net/lesson/asp-net-core-launchsettings-json-file/)
