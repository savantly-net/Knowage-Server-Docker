FROM alpine/git:v2.26.2-amd64 AS build

WORKDIR /app

ARG KNOWAGE_APP_BRANCH

# Depth
RUN git clone --depth 1 --branch $KNOWAGE_APP_BRANCH https://github.com/KnowageLabs/Knowage-Server.git

FROM r-base:4.0.3

WORKDIR /app

COPY --from=build /app/Knowage-Server/Knowage-R .
COPY LICENSE entrypoint.sh ./

RUN apt-get update \
	&& apt-get install --no-install-recommends -y libssl-dev file \
	&& rm -rf /var/lib/apt/lists/*

# It takes too long: makes a separate layer
RUN r -e "install.packages('plumber')"

RUN r -e "install.packages('base64enc')" \
	&& r -e "install.packages('jose')" \
	&& chmod a+x *.sh

EXPOSE 5001/tcp

ENTRYPOINT [ "./entrypoint.sh" ]

CMD [ "r","knowage-r.R" ]