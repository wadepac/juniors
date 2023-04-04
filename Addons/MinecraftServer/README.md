## Minecraft Server

### В контейнерах docker
1. [Установим docker и docker-compose](../../DevOps/Docker/README.md)
2. Создадим директорию /etc/minecraft
```
mkdir /etc/minecraft
```
3. Назначим на нее полные права всем пользователям на редактирование:
```
chmod 777 -R /etc/minecraft
```
4. Созадим файл docker-compose.yaml в /etc/minecraft:
```
volumes:
  minecraft-data:

services:
  minecraft:
    # Image tag (replace with yours)
    image: itzg/minecraft-server:java17-openj9
    volumes:
      - minecraft-data:/data
      - ./plugins:/plugins:ro
    environment:
      EULA: 'true'
      CONSOLE: 'false'
      # Minecraft server type (replace with yours)
      # VANILLA, FORGE, BUKKIT, SPIGOT, PAPER, CURSEFORGE, SPONGEVANILLA, FABRIC
      TYPE: PAPER
      # Paper Minecraft server version (replace with yours)
      VERSION: LATEST
      # Memory limit (replace with yours)
      MEMORY: 2G
      # Server name (replace with yours)
      SERVER_NAME: Jun39
      # Message of the day (replace with yours)
      MOTD: Hi there!
      # Level type and generator settings (replace with yours)
      # DEFAULT, FLAT, LARGEBIOMES, AMPLIFIED, CUSTOMIZED, BUFFET
      LEVEL_TYPE: DEFAULT
      # World save name (replace with yours)
      LEVEL: minecraft-world-1
      # Game mode (replace with yours)
      # creative, survival, adventure, spectator (replace with yours)
      MODE: survival
      # Force gamemode (replace with yours)
      FORCE_GAMEMODE: 'false'
      # Difficulty (replace with yours)
      # peaceful, easy, normal, hard
      DIFFICULTY: normal
      # Hardcore (replace with yours)
      HARDCORE: 'false'
      # Announce player achievements (replace with yours)
      ANNOUNCE_PLAYER_ACHIEVEMENTS: 'true'
      # Max players (replace with yours)
      MAX_PLAYERS: 12
      # PVP mode (replace with yours)
      PVP: 'true'
      # Allow flight (replace with yours)
      ALLOW_FLIGHT: 'false'
      # Max world size (replace with yours)
      MAX_WORLD_SIZE: 10000
      # Max build height (replace with yours)
      MAX_BUILD_HEIGHT: 265
      # Spawn animals (replace with yours)
      SPAWN_ANIMALS: 'true'
      # Spawn monsters (replace with yours)
      SPAWN_MONSTERS: 'true'
      # Spawn NPCs (replace with yours)
      SPAWN_NPCS: 'true'
      # Set spawn protection (replace with yours)
      SPAWN_PROTECTION: 0
      # View distance (replace with yours)
      VIEW_DISTANCE: 10
      # Online mode (replace with yours)
      ONLINE_MODE: 'true'
      # Snooper (replace with yours)
      SNOOPER_ENABLED: 'true'
      # Enable command block (replace with yours)
      ENABLE_COMMAND_BLOCK: 'true'
    ports:
      - "25565:25565"
    restart: unless-stopped
```
5. Запустим наш контейнер, находясь в директории /etc/minecraft:
```
docker-compose up -d 
```