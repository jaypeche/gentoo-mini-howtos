# Mini_Howto Install chatGPT into Windows PowerShell 2024 

## Installation of Python3_12
`
PS mkdir 'C:\Program Files\Python3_12\'  
`

`
PS cd 'C:\Program Files\Python3_12\'   
`

`
PS curl https://www.python.org/ftp/python/3.12.6/python-3.12.6-amd64.exe -o python-3.12.6-amd64.exe  
`

`
PS .\python-3.12.6-amd64.exe  
`

## Check Python pip version 

`
PS python --version  
`
 
`
PS pip --version
`

## Create directory for virtualenv

`
PS mkdir 'C:\Users\your_user\chatGPT\'  
`  

`
PS cd 'C:\Users\your_user\chatGPT\'  
`

## Create virtualenv for ChatGPT client

`
PS python -m venv 'C:\Users\your_user\chatGPT\'
`

## ChatGPT virtualenv must be populate like this :

`
PS ls 'C:\Users\your_user\chatGPT\'
`

Mode                 LastWriteTime         Length Name  
 ----                 -------------         ------ ----  
d-----        09/09/2024     22:32                Include  
d-----        09/09/2024     22:32                Lib  
d-----        09/09/2024     22:33                Scripts  
-a----        09/09/2024     22:32            262 pyvenv.cfg  

## After creating OPENAI_API_KEY on https://platform.openai.com/, define them like this :

`
PS [System.Environment]::SetEnvironmentVariable('OPENAI_API_KEY', 'your_api_key', [System.EnvironmentVariableTarget]::User)
`

## Define PATH for Python312 Scripts (recommanded) :

`
PS $env:Path += ";C:\Users\your_user\AppData\Roaming\Python\Python312\Scripts\"
`

## Autorize Shell execution locally

`
PS Powershell -ExecutionPolicy UnRestricted -command "C:\Users\your_user\chatGPT\Scripts\Activate.ps1"  
`

`
PS Powershell -ExecutionPolicy UnRestricted -command "C:\Users\your_user\chatGPT\Scripts\deactivate.bat"
`

## Or Autorize Shell execution globally if not working (Be Careful !)

`
PS Set-ExecutionPolicy Unrestricted
`

## Install ShellGPT client :

`
PS pip.exe install shell-gpt
`

## Loading chatGPT into terminal :

`
PS C:\Users\your_user\chatGPT\Scripts\Activate.ps1
`

## Close chatGPT session like this :

`
PS C:\Users\your_user\chatGPT\Scripts\deactivate.bat
`

## For Shell dialog with GPT OpenAI

`
PS sgpt.exe "Write Hello World in bash"
`

Enjoy !

Sources:  	https://www.moyens.net/comment/comment-configurer-et-utiliser-chatgpt-dans-le-terminal-linux/  
			https://python.land/virtual-environments/virtualenv  
			https://platform.openai.com/docs/overview   
			https://www.python.org/doc/  
			
