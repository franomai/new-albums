# New Albums [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](https://makeapullrequest.com)

A python script to update a Spotify playlist every day with all the songs from any significant new albums. It shouldn't include single-only releases. Anything older than a month should be deleted.

## Installation

Before you can run the `New Albums` script, there are some pre-requisites that are assumed.

### Spotify Developer Account

The script will need a Spotify **Client Id** and **Client Secret** to interact with Spotify's Web API.

Register for a [developer account](https://developer.spotify.com) on Spotify. A free account is sufficient for a developer account. After registering, create a new app through the [dashboard](https://developer.spotify.com/dashboard).

Once you create a new app, a Client Id and Client Secret will be generated and available on the page for your new app. You will need these in later steps.

Additionally, the `New Albums` script uses an [Authorization Code Flow](https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow). Due to this, you will need to set a redirect URL for your app.

To add a redirect URL, open your new app's settings via the [developer dashboard](https://developer.spotify.com/dashboard/applications). Add `https://localhost:8080` as a redirect URI. You may also specify a different (or multiple) local redirect URIs, but you will have to make sure to set the correct URI as an environment variable as explained below.

### Spotify Playlist Id

The script will need the unique ID for one of your playlists. To get the ID for a playlist, in Spotify, right-click on the playlist > Share > Copy Share Link. The link will contain the playlist ID. It is the string between `playlist/` and `?si=`.

This string will be used for the `NEW_ALBUMS_PLAYLIST_ID` environment variable explained below.

### Environment Variables

The following environment variables are mandatory:

- `SPOTIFY_CLIENT_ID`
- `SPOTIFY_CLIENT_SECRET`
- `SPOTIFY_USER`
- `NEW_ALBUMS_PLAYLIST_ID`

A redirect URI may be set with the optional envvar `SPOTIFY_REDIRECT_URI` - this defaults to `https://localhost:8080` if unset.

#### .env

The recommended way for setting these environmental variables is to use a `.env` file.

A `.env` file is a file in the root directory of this project or at the root of your home directory that lists the environment variables above. For example:

```shell
SPOTIFY_CLIENT_ID=id
SPOTIFY_CLIENT_SECRET=secret
SPOTIFY_USER=userid
NEW_ALBUMS_PLAYLIST_ID=playlistid
```

#### Windows

FIRST SET UP ENVIRONMENT VARIABLES ON YOUR COMPUTER, [SEE HERE FOR INSTRUCTIONS](https://superuser.com/questions/949560/how-do-i-set-system-environment-variables-in-windows-10).

To set all 4 in a one-liner on Windows:

```shell
set SPOTIFY_CLIENT_ID=xxx && set SPOTIFY_CLIENT_SECRET=xxx && set SPOTIFY_REDIRECT_URI=http://localhost:8080 && set NEW_ALBUMS_PLAYLIST_ID=xxx && set SPOTIFY_USER=xxx
```

To view all currently set environment variables, use the command `set`.

#### Unix (*BSD, Linux, MacOS)

To set all 4 in a one-liner on Linux: ([Instructions](https://www.serverlab.ca/tutorials/linux/administration-linux/how-to-set-environment-variables-in-linux/))

```shell
 export SPOTIFY_CLIENT_ID=xxx && export SPOTIFY_CLIENT_SECRET=xxx && export SPOTIFY_REDIRECT_URI='http://localhost:8080' && export NEW_ALBUMS_PLAYLIST_ID=xxx && export SPOTIFY_USER=xxx
```

Note that there is a space before the first export.  This is intentional and should not be removed: a leading space in most terminals instructs the terminal not to lodge the command in the history file, leaving fewer traces of your environment keys in side effecting channels.

To view all currently set environment variables, use the command `env`. You can filter down to just the above using `env | egrep '(SPOTIFY|PLAYLIST)'`

### Set which genres to Reject
You can add a custom "reject.txt" file in the `new_albums` folder.

The script will reject any album on which the primary artist's first genre matches any of the genres in your reject list. For example, if you have `"dance pop"` in your reject list, then the script will reject Beyoncé's 'RENAISSANCE' album, because her first genre is dance pop. (Her genres are ['dance pop', 'pop', 'r&b']).

### Set which genres to Accept regardless of Genre rejects
You can add a custom "accept.txt" file in the `new_albums` folder.


### Create a Virtual Environment (optional)

These steps are not necessary, but recommended for build environment isolation. You could use [virtualenv](https://virtualenv.pypa.io/en/latest/installation.html) (and [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/index.html)) or just the built-in [venv](https://docs.python.org/3/library/venv.html).

`New Albums` also supports [poetry](https://python-poetry.org), a modern dependency management and virtual environment tool. You will need to install `poetry` as explained on its site to use it with `New Albums`.

### Install dependencies

Installing the package resolves and builds the dependencies required to run `New Albums`. There are two ways to install the dependencies. You may use the traditional `setup.py` method _or_ `poetry`.

Traditional:

```shell
pip install .
```

_Or_ poetry:

```shell
poetry install
```

### Local development

The package can be installed in developement (editable) mode to allow you to make changes locally.

Traditional:

```shell
pip install -e .
```

Poetry automatically installs dependencies in development mode.

## Running

Once you have completed all the installation steps, run `New Albums` script by running either:

Traditional:

```shell
py -m new_albums
```

Poetry:

```shell
poetry run python -m new_albums
```

### Add to rejects.txt or accept.txt with command line args

- `py -m new_albums -r "florida rap"`: will reject any album with the primary artist's first genre as "atlanta rap"
- `py -m new_albums -r "Kodak Black"`: will add any album by the artist's even if their first genre is in your reject list

### Filter by country

By default, the script will only pull new releases from the United States. To filter by specific countries, you can pass the `--country` flag followed by one or more country ISO codes, e.g. `py -m new_albums --country JP`

**Options**:
- `py -m new_albums`: filters by US
- `py -m new_albums -c us jp`: filters by US and JP
- `py -m new_albums --country GB`: filter by GB
- `py -m new_albums --country list`: list all available Spotify country codes.
- `py -m new_albums --country all`: worldwide

`-c` can also be used as an abbreviation for `--country`, e.g. `py -m new_albums -c all`

### Filter by top genres

By default, the script will not filter by top genres. To enable this filter, you can pass the `--top-genres` flag, e.g. `py -m new_albums --top-genres`

`-g` can also be used as an abbreviation for `--top-genres`, e.g. `py -m new_albums -g`

### Specify an fiat file

`New Albums` defaults to using `_default_fiat` as a fiat file. The option `--fiat` or `-f` for short allows using an alternative fiat file.

For example, to run `New Albums` with `prog_fiat` as a fiat file:

```shell
poetry run python -m new_albums -f prog_fiat
```

Or:
```shell
py -m new_albums -f prog_fiat
```

### Logging granularity

The `--log` or `-l` argument can be used to specify the logging level.

**Valid levels:**

* debug
* info
* warning
* critical
* error

Usage:

```shell
poetry run python -m new_albums -l critical
```

### Limit releases

Use the `--limit` or `-m` argument if you'd like to limit the amount of new releases returned. Defaults to 20 and must be > 0 or <= 50.

```shell
poetry run python -m new_albums -m 50
```

### Timeout

`--timeout` or `-t` sets the amount of seconds to wait for a response before failing. Defaults to 20.

```shell
poetry run python -m new_albums -t 5
```
