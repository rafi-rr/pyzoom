![Zoom Logo](https://d24cgw3uvb9a9h.cloudfront.net/static/93946/image/new/ZoomLogo.png)

**WARNING: Version 1.0.0 introduces breaking change. The library now only supports OAUTH tokens, since Zoom is deprecating the JWT support as of June 1, 2023**

Also the project now uses [break versioning](https://github.com/ptaoussanis/encore/blob/master/BREAK-VERSIONING.md), meaning that upgrading from 1.0.x to 1.0.y will always be safe, upgrade to 1.y.0 might break something small, and upgrade to y.0.0. will break almost everything. That was a versioning spec in one sentence, by the way.

# Python wrapper for Zoom API
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/pyzoom)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![PyPI](https://img.shields.io/pypi/v/pyzoom)](https://pypi.org/project/pyzoom/)
![PyPI - License](https://img.shields.io/pypi/l/pyzoom)
![PyPI - Downloads](https://img.shields.io/pypi/dw/pyzoom)
[![](https://img.shields.io/badge/Support-Buy_coffee!-Orange)](https://www.buymeacoffee.com/licht1stein)


**This library requires contributors and maintainers since Python stopped being my primary language a couple of years ago.**
I do use it in some of my older projects, so I have to update it from time to time.

Links:
* [Api Reference](https://marketplace.zoom.us/docs/api-reference)
* [Using Zoom API](https://marketplace.zoom.us/docs/api-reference/using-zoom-apis)

## Installation

Using pip:

`pip install -U pyzoom`

Using [poetry](https://python-poetry.org/):

`poetry add pyzoom`

## OAUTH Authorization
I have provided some functions to ease the flow of getting all the tokens. You will need your Zoom App's `Client ID` and `Client Secret`.

You can find out more details by reading the docstrings of `request_tokens` and `refresh_tokens`. And remember, the library does not handle the first step for you, namely creating the redirect URL and capturing the code from the redirect.

### Requesting Tokens
Once your user has accepted integration on the zoom side and you received the code from the redirect:

```python
from pyzoom import request_tokens

tokens = request_tokens("APP_CLIENT_ID", "APP_CLIENT_SECRET", "APP_REDIRECT_URL", "CALLBACK_CODE"):
```
The result of a successful request will be a map with the tokens. 

### Refreshing tokens

```python
from pyzoom import refresh_tokens

tokens = refresh_tokens("APP_CLIEN_ID", "APP_CLIENT_SECRET", "USER_REFRESH_TOKEN")
```
The result of a successful request will be a map with the new tokens. Remember, that the refresh token will also be updated, which will invalidate the token you just used. 

## Usage

### Basic instantiation:

```python
from pyzoom import ZoomClient

client = ZoomClient('YOUR_ZOOM_ACCESS_TOKEN')
```

Optionally you can specify a different base URL either upon instantiation or any time later:

```python
client = ZoomClient ('YOU_ZOOM_ACCCESS_TOKEN', base_url="https://api.zoomgov.us/v2")
```

### Instantiation from environment variables

You can also create an instance of client when access key in environment variables `ZOOM_ACCESS_TOKEN`. *Since the access token expires after one hour, this method is not a good idea any more.*

```python
from pyzoom import ZoomClient

client = ZoomClient.from_environment()
```


### Meetings

#### Create meeting, update meeting and add registrant
```python
from pyzoom import ZoomClient
from datetime import datetime as dt

client = ZoomClient.from_environment()

# Creating a meeting
meeting = client.meetings.create_meeting('Auto created 1', start_time=dt.now().isoformat(), duration_min=60, password='not-secure')

# Update a meeting
meeting = client.meetings.update_meeting('Auto updated 1', meeting_id = meeting.id ,start_time=dt.now().isoformat(), duration_min=60,password='not-secure')

# Adding registrants
client.meetings.add_meeting_registrant(meeting.id, first_name='John', last_name='Doe', email='john.doe@example.com')
```
You can use `client.meetings.add_and_confirm_registrant` to also confirm auto added
registrants to a closed meeting.

### Raw API methods

You can also use the library for making raw requests to the API:

```python
from pyzoom import ZoomClient

client = ZoomClient.from_environment()

# Get self
response = client.raw.get('/users/me')

# Get all pages of meeting participants
result_dict = client.raw.get_all_pages('/past_meetings/{meetingUUID}/participants')
```

### Packaging notice
This project uses the excellent [poetry](https://python-poetry.org) for packaging. Please read about it and let's all start using
`pyproject.toml` files as a standard. Read more:

* [PEP 518 -- Specifying Minimum Build System Requirements for Python Projects](https://www.python.org/dev/peps/pep-0518/)

* [What the heck is pyproject.toml?](https://snarky.ca/what-the-heck-is-pyproject-toml/)

* [Clarifying PEP 518 (a.k.a. pyproject.toml)](https://snarky.ca/clarifying-pep-518/)


### Support

<a href="https://www.buymeacoffee.com/licht1stein" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" style="height: 30px !important;width: 130px !important;" ></a>

### Disclaimer
This library is not related to Zoom Video Communications, Inc. It's an open-source project that 
aims to simplify working with this suddenly very popular service.
