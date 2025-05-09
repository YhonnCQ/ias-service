sudo mkdir -p /opt/iasservice
sudo cp /ruta/de/IASServiceClientCore /opt/iasservice/
sudo cp -R /Volumes/TU_USB/publish/* /opt/iasservice/
sudo chmod +x /opt/iasservice/IASServiceClientCore

launchctl load ~/Library/LaunchAgents/com.example.iasservice.plist

launchctl list | grep iasservice
