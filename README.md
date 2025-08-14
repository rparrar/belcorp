### WSUS resetear BD Windows Update.
```sh
docker exec -it contenedor salt 'servidor' service.stop wuauserv
docker exec -it contenedor salt 'servidor' service.start wuauserv
docker exec -it contenedor salt 'servidor' cmd.run "wuauclt.exe /resetauthorization"
docker exec -it contenedor salt 'servidor' cmd.run "wuauclt.exe /r /detectnow
docker exec -it contenedor salt 'servidor' cmd.run "wuauclt.exe /r /detectnow"
docker exec -it contenedor salt 'servidor' cmd.run "wuauclt.exe /reportnow"
```
### Obtener KBS instalados en un servidor.
```sh
docker exec -it contenedor salt 'servidor' cmd.run 'wmic qfe get HotFixID,Description,InstalledOn'
docker exec -it contenedor salt 'servidor' cmd.run "'powershell.exe \\"Get-WmiObject -Class Win32_QuickFixEngineering | Select-Object Description, HotFixID, InstalledOn\\"'"
docker exec -it contenedor salt 'servidor' cmd.run "powershell.exe Get-HotFix" => aplica para Win Server 2025 Datacenter, por deprecación WMIC.
```
### Obtener KBs disponibles para instalación en un servidor, sin instalarlos, varía la ultima flag "install=true/false".
```sh
docker exec -i contenedor salt 'servidor' win_wua.list categories=["Security Updates","Critical Updates"] install=false
docker exec -i contenedor salt 'servidor' win_wua.list severities=["Critical","Important"] install=false
```
### TroubleShooting revisión de parches disponibles.
```sh
docker exec -it contenedor salt 'servidor' cmd.run "net stop wuauserv && net stop bits"
docker exec -it contenedor salt 'servidor' cmd.run "del /q /s C:\\Windows\\SoftwareDistribution\\Download\\* && del /q /s C:\\Windows\\SoftwareDistribution\\DataStore\\*"
docker exec -it contenedor salt 'servidor' cmd.run "net start wuauserv && net start bits"
docker exec -it contenedor salt 'servidor' sc query wuauserv
docker exec -it contenedor salt 'servidor' sc query bits

```
### Reinicio de servidor desde Salt.
```sh
docker exec -it contenedor salt 'servidor' cmd.run system.reboot 5 True
docker exec -it contenedor salt 'servidor' system.restart
docker exec -it contenedor salt 'servidor' cmd.run "shutdown /r"
```
### Obtener información completa de un servidor.
```sh
docker exec -it NGIT-BEL-SM-PROD salt BEL_AWNTS511 cmd.run systeminfo
```
### Entrega información de variable en registro de Windows.
```sh
reg.read_value HKLM "SOFTWARE\\Policies\\Microsoft\\Windows\\WindowsUpdate\\AU" "UseWUServer"
```
---
# Aplicación de scripts externos Python
## Cambiar lugar de obtención de parches de seguridad
Establecer como internet el origen
```sh
python3 wsus_belcorp_0.py servidor
```
Establecer como servidor WSUS el origen
```sh
python3 wsus_belcorp_1.py servidor
```

## Lista todos los parches disponibles para instalación en el servidor
```sh
python3 wsus_patch_belcorp.py servidor
```
