# Self-Hosted KMS

Configure and deploy a local KMS server.

## Server Setup

1. Setup an Ubuntu server VM with the docker snap package

2. Pull the docker images for the server
```shell
sudo docker pull 11notes/kms:1.0.3 
```
```shell
sudo docker pull 11notes/kms-gui:1.0.3
```
3. Create a docker-compose.yml file

4. Paste the following:

```yaml
name: "kms"
services:
  app:
    image: "11notes/kms:1.0.3"
    environment:
      TZ: "America/New_York"
    volumes:
      - "var:/kms/var"
    networks:
      frontend:
    ports:
      - "1689:1688/tcp"
    restart: "always"

  gui:
    image: "11notes/kms-gui:1.0.3"
    depends_on:
      app:
        condition: "service_healthy"
        restart: true
    environment:
      TZ: "America/New_York"
    volumes:
      - "var:/kms/var"
    networks:
      frontend:
    ports:
      - "3000:3000/tcp"
    restart: "always"

volumes:
  var:

networks:
  frontend:
```

## Installing License

- Easy way (might not work)
```shell
cscript slmgr.vbs /ipk PRODUCT_KEY
```

- Upgrading from eval to full version (what i had to do)
```shell
DISM /online /Set-Edition:EDITION_LABEL /ProductKey:PRODUCT_KEY /AcceptEula
```

## Activating Server (via slmgr)

1. Add your KMS server

```shell
cscript slmgr.vbs /skms ip:port
```

2. Activate KMS server

```shell
cscript slmgr.vbs /ato
```

3. Verify activation

```shell
cscript slmgr.vbs /dlv
```

## Activating Server (via Registry)

1. Add your KMS server information to server via registry

NOTE: Replace **KMS_IP** and **KMS_PORT**

```shell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform" -Name "KeyManagementServiceName" -Value "KMS_IP"

Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform" -Name "KeyManagementServicePort" -Value "KMS_PORT"

Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\OfficeSoftwareProtectionPlatform" -Name "KeyManagementServiceName" -Value "KMS_IP"

Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\OfficeSoftwareProtectionPlatform" -Name "KeyManagementServicePort" -Value "KMS_PORT"
```

2. Activate KMS server

```shell
cscript slmgr.vbs /ato
```