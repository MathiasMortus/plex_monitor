# plex_monitor

## What is this?

A lightweight automation tool that monitors your Plex watchlist and automatically adds:
- **TV shows** to Sonarr
- **Movies** to Radarr

This is a fork of plex_debrid that has been completely refactored to remove all debrid and scraper functionality, focusing solely on Plex watchlist → Sonarr/Radarr integration.

## How does it work?

1. Monitors your Plex watchlist (and optionally Trakt lists or Overseerr requests)
2. When you add something to your watchlist:
   - TV shows are automatically added to Sonarr with monitoring enabled
   - Movies are automatically added to Radarr with monitoring enabled
3. Sonarr/Radarr handle finding and downloading the content through their configured indexers
4. Optionally removes items from watchlist after successfully adding to Sonarr/Radarr

## Requirements

- Python 3.7+
- Plex account with watchlist access
- Sonarr v3+ (for TV shows)
- Radarr v3+ (for movies)
- Sonarr/Radarr must be configured with:
  - At least one indexer (Jackett, Prowlarr, etc.)
  - Quality profiles
  - Root folders

## Installation

```bash
git clone https://github.com/yourusername/plex_monitor.git
cd plex_monitor
pip install -r requirements.txt
python main.py
```

## Configuration

On first run, you'll be prompted to configure:

### Content Services
- **Plex Watchlist**: Your Plex token for watchlist access
- **Trakt** (optional): Trakt authentication for additional watchlist sources
- **Overseerr** (optional): Overseerr API for request monitoring

### Sonarr Configuration
- **Base URL**: e.g., `http://localhost:8989` or `http://sonarr:8989`
- **API Key**: Found in Sonarr under Settings → General → Security
- **Quality Profile ID**: The ID of the quality profile to use (default: 1)
- **Root Folder**: Where TV shows will be stored (e.g., `/data/media/tv`)

### Radarr Configuration
- **Base URL**: e.g., `http://localhost:7878` or `http://radarr:7878`
- **API Key**: Found in Radarr under Settings → General → Security
- **Quality Profile ID**: The ID of the quality profile to use (default: 1)
- **Root Folder**: Where movies will be stored (e.g., `/data/media/movies`)

### Library Services
- **Library Collection Service**: Choose Plex or Jellyfin to check existing library
- **Library Update Services**: Optional library refresh after adding content

## Usage

### Option 1: Web UI Configuration (Recommended for Docker/Unraid)

Start the web configuration interface:

```bash
python webui.py
```

Then open your browser to `http://localhost:5000` to configure plex_monitor through a user-friendly web interface.

Features:
- ✅ Easy form-based configuration
- ✅ Test Sonarr/Radarr connections
- ✅ View configuration status
- ✅ Perfect for Docker/Unraid deployments

### Option 2: Terminal Menu

For local/interactive setups:

```bash
python main.py
```

Menu Options:
1. **Run**: Start monitoring watchlists
2. **Settings**: Configure Sonarr, Radarr, Plex, and other services
3. **Ignored Media**: View and manage ignored items

### Option 3: Service Mode (Background)

For automated/production use:

```bash
python main.py -service
```

This runs continuously without user interaction, perfect for Docker containers.

## Features

- ✅ Automatic watchlist monitoring (default: every 30 seconds)
- ✅ Sonarr integration for TV shows
- ✅ Radarr integration for movies
- ✅ Library checking to avoid duplicates
- ✅ Auto-remove from watchlist after adding (configurable)
- ✅ Support for Plex, Trakt, and Overseerr watchlists
- ✅ Jellyfin library support

## Key Changes from plex_debrid

This fork has been completely refactored:

### Removed
- ❌ All debrid services (Real-Debrid, AllDebrid, Premiumize, etc.)
- ❌ All torrent scrapers (RarBG, Jackett, Prowlarr, Torrentio, etc.)
- ❌ Release quality management and version filtering
- ❌ Manual scraper interface

### Added
- ✅ Sonarr API integration
- ✅ Radarr API integration
- ✅ Simplified workflow focused on watchlist → arr services

## Migrating from plex_debrid 2.x

If you're migrating from the original plex_debrid:

1. **Backup** your current `settings.json`
2. Pull the latest changes
3. Run `python main.py` - you'll be prompted to reconfigure
4. Your Plex and Trakt watchlist settings will be preserved
5. You'll need to configure Sonarr and Radarr settings

## Troubleshooting

### "Sonarr: Not configured"
- Ensure you've entered the Sonarr base URL and API key in settings
- Test connectivity: Settings → Arr Services → Sonarr Base URL

### "No TVDB ID found"
- Plex metadata may be incomplete
- Try refreshing metadata in Plex for that show

### Items not being added
- Check that items have been released (not future releases)
- Verify library checking isn't marking them as already collected
- Check debug logs: Settings → UI Settings → Debug printing → true

## Docker / Unraid Setup

### Initial Setup with Web UI

1. **Start the web configuration interface**:
   ```bash
   docker run -d \
     --name plex-monitor-config \
     -p 5000:5000 \
     -v /path/to/config:/app \
     your-image python webui.py
   ```

2. **Configure via browser**:
   - Open `http://your-server:5000`
   - Enter your Plex token, Sonarr/Radarr URLs and API keys
   - Test connections
   - Save configuration

3. **Stop the web UI and start the service**:
   ```bash
   docker stop plex-monitor-config
   docker run -d \
     --name plex-monitor \
     --restart unless-stopped \
     -v /path/to/config:/app \
     your-image python main.py -service
   ```

### Unraid Template

```xml
<Container version="2">
  <Name>plex-monitor</Name>
  <Repository>your-image</Repository>
  <Config Name="Config Directory" Target="/app" Default="/mnt/user/appdata/plex_monitor" Mode="rw" Description="Config directory" Type="Path" Display="always" Required="true" Mask="false">/mnt/user/appdata/plex_monitor</Config>
  <Config Name="Web UI Port (for setup)" Target="5000" Default="5000" Mode="tcp" Description="Web UI port" Type="Port" Display="always" Required="false" Mask="false">5000</Config>
  <PostArgs>python main.py -service</PostArgs>
</Container>
```

**Setup Steps**:
1. Install the container with `PostArgs: python webui.py`
2. Open Web UI at `http://your-server:5000` and configure
3. Stop container
4. Change `PostArgs` to `python main.py -service`
5. Start container - it will now run in background monitoring mode

## Environment Variables

For Docker/automated deployments:

- `CLIENT_ID`: Trakt OAuth client ID (if using Trakt)
- `CLIENT_SECRET`: Trakt OAuth client secret (if using Trakt)

## Support

This is a community fork. For issues or questions, please open a GitHub issue.

## Thanks to these contributors!

<!-- readme: collaborators,contributors -start -->
<table>
	<tbody>
		<tr>
            <td align="center">
                <a href="https://github.com/itsToggle">
                    <img src="https://avatars.githubusercontent.com/u/71379623?v=4" width="64;" alt="itsToggle"/>
                    <br />
                    <sub><b>itsToggle</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/funkypenguin">
                    <img src="https://avatars.githubusercontent.com/u/1524686?v=4" width="64;" alt="funkypenguin"/>
                    <br />
                    <sub><b>funkypenguin</b></sub>
                </a>
            </td>
			<td align="center">
                <a href="https://github.com/mathiasmortus">
                    <img src="https://avatars.githubusercontent.com/u/77238760?v=4" width="64;" alt="mortus"/>
                    <br />
                    <sub><b>mortus</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/sirstudly">
                    <img src="https://avatars.githubusercontent.com/u/12377354?v=4" width="64;" alt="sirstudly"/>
                    <br />
                    <sub><b>sirstudly</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/themegaphoenix">
                    <img src="https://avatars.githubusercontent.com/u/9766462?v=4" width="64;" alt="themegaphoenix"/>
                    <br />
                    <sub><b>themegaphoenix</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/KaceCottam">
                    <img src="https://avatars.githubusercontent.com/u/28381193?v=4" width="64;" alt="KaceCottam"/>
                    <br />
                    <sub><b>KaceCottam</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/KamalF">
                    <img src="https://avatars.githubusercontent.com/u/8170277?v=4" width="64;" alt="KamalF"/>
                    <br />
                    <sub><b>KamalF</b></sub>
                </a>
            </td>
		</tr>
		<tr>
            <td align="center">
                <a href="https://github.com/maspuce">
                    <img src="https://avatars.githubusercontent.com/u/688714?v=4" width="64;" alt="maspuce"/>
                    <br />
                    <sub><b>maspuce</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/Murmiration">
                    <img src="https://avatars.githubusercontent.com/u/26490372?v=4" width="64;" alt="Murmiration"/>
                    <br />
                    <sub><b>Murmiration</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/codevski">
                    <img src="https://avatars.githubusercontent.com/u/1435321?v=4" width="64;" alt="codevski"/>
                    <br />
                    <sub><b>codevski</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/kana2001">
                    <img src="https://avatars.githubusercontent.com/u/71416354?v=4" width="64;" alt="kana2001"/>
                    <br />
                    <sub><b>kana2001</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/master131">
                    <img src="https://avatars.githubusercontent.com/u/1592009?v=4" width="64;" alt="master131"/>
                    <br />
                    <sub><b>master131</b></sub>
                </a>
            </td>
            <td align="center">
                <a href="https://github.com/mcorcoran">
                    <img src="https://avatars.githubusercontent.com/u/1950615?v=4" width="64;" alt="mcorcoran"/>
                    <br />
                    <sub><b>mcorcoran</b></sub>
                </a>
            </td>
		</tr>
		<tr>
            <td align="center">
                <a href="https://github.com/piratsch">
                    <img src="https://avatars.githubusercontent.com/u/106690882?v=4" width="64;" alt="piratsch"/>
                    <br />
                    <sub><b>piratsch</b></sub>
                </a>
            </td>
		</tr>
	<tbody>
</table>
<!-- readme: collaborators,contributors -end -->
