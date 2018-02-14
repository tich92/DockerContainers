#Based on Windows Server Core
FROM microsoft/windowsservercore:1709

LABEL Description="RabbitMQ" Vendor="Ciklum" Version="3.6.15"

# Erlang definition
# Latest stable version of RabbitMQ server
# Path to RabbitMQ configuration
ENV ERLANG_HOME="c:\\erlang" \
	RABBITMQ_VERSION="3.6.15" \
	RABBITMQ_CONFIG_FILE="c:\\rabbitmq"
	
SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

EXPOSE 4369 5671 5672 25672

RUN Set-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name ServerPriorityTimeLimit -Value 0 -Type DWord;

# download and install erlang using silent install option, and remove installer when done
# download and extract rabbitmq, and remove zip file when done
# remove version from rabbitmq folder name
RUN Invoke-WebRequest -Uri "http://www.erlang.org/download/otp_win64_20.1.exe" -OutFile "c:\\erlang_install.exe" ; \
        Start-Process -Wait -FilePath "c:\\erlang_install.exe" -ArgumentList /S, /D=$env:ERLANG_HOME ; \
        Remove-Item -Force -Path "C:\\erlang_install.exe" ; \
    Invoke-WebRequest -Uri "https://www.rabbitmq.com/releases/rabbitmq-server/v$env:RABBITMQ_VERSION/rabbitmq-server-windows-$env:RABBITMQ_VERSION.zip" -OutFile "c:\\rabbitmq.zip" ; \
        Expand-Archive -Path "c:\\rabbitmq.zip" -DestinationPath "c:\\" ; \
        Remove-Item -Force -Path "c:\\rabbitmq.zip" ; \
    Rename-Item -Path "c:\\rabbitmq_server-$env:rabbitmq_version" -NewName "c:\\rabbitmq"


# Create definitions file
COPY rabbitmq.config ./
COPY definitions.json ./

# enable management plugin
RUN c:\rabbitmq\sbin\rabbitmq-plugins.bat enable rabbitmq_management --offline

CMD c:\rabbitmq\sbin\rabbitmq-server.bat
