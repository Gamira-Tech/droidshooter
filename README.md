# Droidshooter · Gamira client

Client integration patches for [Droidshooter](https://github.com/googleforgames/global-multiplayer-demo),
with platform authentication and direct Gamira profile, region and matchmaking requests.

## Apply the patch

Run these commands from a new working directory. Git and tar are required.

```bash
git clone https://github.com/Gamira-Tech/droidshooter.git droidshooter-patches
git clone https://github.com/googleforgames/global-multiplayer-demo.git droidshooter-upstream
mkdir -p droidshooter-client/global-multiplayer-demo
git -C droidshooter-upstream archive b5465e81c9a5121660182702ca81a84358658c58 \
  LICENSE game | tar -x -C droidshooter-client/global-multiplayer-demo \
  --exclude='*.yaml' --exclude='*.yml' --exclude='Dockerfile' \
  --exclude='*Server.Target.cs' --exclude='*.dll' --exclude='*.exe' \
  --exclude='*.so' --exclude='*.pdb' --exclude='*.pak' \
  --exclude='game/unreal-engine' --exclude='.gcloudignore'
cd droidshooter-client
git apply --check ../droidshooter-patches/patches/0001-gamira-integration.patch
git apply ../droidshooter-patches/patches/0001-gamira-integration.patch
```

The upstream revision and patch checksum are recorded in [upstream.json](upstream.json).

## Configure and run

Build the Unreal Engine 5 client from
`global-multiplayer-demo/game/Droidshooter.uproject` using the upstream engine workflow.
The source tree retains the upstream shared gameplay code and engine dependencies.

In `global-multiplayer-demo/game/GameLauncher/`, copy `app.ini.sample` to `app.ini`.
Set these two public service URLs:

```ini
gamira_url = https://gamira.example.com
platform_url = https://platform.example.com
callback_listen_port = 8083
```

Use the authentication platform's base URL without `/api/v1`. In the same file,
set the `binary` entry for your operating system to the built client executable.
Keep the callback port consistent with the authentication platform's redirect settings.

With Go and Fyne's desktop build dependencies installed, run from `GameLauncher/`:

```bash
go build -o launcher .
./launcher
```

Sign in through the launcher and open the game. The client uses game mode
`gm_arena`, configured in Gamira. Game servers and the two services are operated
separately. The native game calls Gamira directly for matchmaking.

Patch reconstruction and the Go launcher build have been checked. The native
Unreal client still requires an engine build and live matchmaking validation.

## Credits

Original sample: Google and contributors, Apache-2.0. See [CREDITS.md](CREDITS.md).
Unreal Engine and upstream dependencies retain their own terms and notices.
