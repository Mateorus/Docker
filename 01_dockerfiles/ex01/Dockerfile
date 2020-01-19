FROM 		debian:latest
EXPOSE		9987/udp 10011 30033
WORKDIR		/teamspeak
RUN			apt-get update && \
			apt-get upgrade -y && \
			apt-get install -y openssl bzip2 curl && \
			curl "https://files.teamspeak-services.com/releases/server/3.11.0/teamspeak3-server_linux_amd64-3.11.0.tar.bz2" --output teamspeak-server.tar.bz2 -s && \
			mkdir teamspeak_server && \
			tar -xvf teamspeak-server.tar.bz2 -C teamspeak_server --strip-components=1
WORKDIR		teamspeak_server
ENTRYPOINT	["./ts3server", "license_accepted=1"]
