#Based on Windows Server Core
FROM microsoft/windowsservercore

LABEL Description="RabbitMQ" Vendor="Ciklum" Version="3.7.3"

# Erlang definition
# Latest stable version of RabbitMQ server
# Path to RabbitMQ configuration
ENV ERLANG_HOME="C:\Program Files\erl9.2" \
	RABBITMQ_VERSION="3.7.3" \
	RABBITMQ_CONFIG_FILE="c:\\rabbitmq"
	
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

EXPOSE 4369 5671 5672 25672

RUN Set-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name ServerPriorityTimeLimit -Value 0 -Type DWord;

#Install Chocolatey 
RUN Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

#It may be the case when Erlang installation was successful but choco may be stuck 
#Install Erlang
RUN choco install erlang -y

# Install cUrl for healthcheck 
RUN choco install curl -y
#Download RabbitMQ server 3.7.3
#Install downloaded RabbitMQ server
#Remove installation file
RUN Invoke-WebRequest -Uri "https://github.com/rabbitmq/rabbitmq-server/releases/download/v$env:RABBITMQ_VERSION/rabbitmq-server-windows-$env:RABBITMQ_VERSION.zip" -OutFile "c:\\rabbitmq.zip" ; \
        Expand-Archive -Path "c:\\rabbitmq.zip" -DestinationPath "c:\\" ; \
        Remove-Item -Force -Path "c:\\rabbitmq.zip" ; \
    Rename-Item -Path "c:\\rabbitmq_server-$env:rabbitmq_version" -NewName "c:\\rabbitmq"

# Create definitions file
COPY rabbitmq.config ./
COPY definitions.json ./
COPY entrypoint.ps1 ./

# enable management plugin
RUN c:\rabbitmq\sbin\rabbitmq-plugins.bat enable rabbitmq_management --offline

CMD c:\rabbitmq\sbin\rabbitmq-server.bat

HEALTHCHECK --timeout=120s CMD curl -f http://localhost:15672 || exit 1