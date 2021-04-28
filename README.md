# Impftermin-Checker
## Setup
### Install dependencies
Install the dependencies with:
```
pip3 install selenium telegram playsound
```

### Install Geckodriver
The Checker uses Selenium with Firefox. For that to work, you need to have the [Geckodriver](https://github.com/mozilla/geckodriver/releases) executable in your PATH. Download the respective binary for your OS and make sure it's accessible (on Linux, just copy it to `/usr/local/bin/`).

### Create `config.json`
Create a `config.json` based on the `config.example.json`.
For each `dataset` entry, you can decide if you already have a Vermittlungscode or not.

#### With Vermittlungscode
If you already have a code, make sure to choose the correct Impfzentrum that your code is valid for (every code is only valid for one single Impfzentrum). The Checker will then enter your code and check if an appointment is available.

For each `dataset` entry with Vermittlungscode, you can optionally state a `min_date` string in the format `YYYY-MM-DD`. This is useful e.g. if you are not yet old enough and want to book an appointment only after you are old enough. If this is present, the Checker will compare all available appointments to the given date.

##### Example:
```
{
   "name": "Erika Mustermann",
   "code": "ABCD-1234-9876",
   "plz": 12345,
   "min_date": "2021-08-31",
   "url": "001-iz.impfterminservice.de",
   "notification_emails": ["erika@mustermann.de", "nospam@example.org"]
}
```

#### Without Vermittlungscode
If you don't have a code yet, please only enter **one** `dataset` per Impfzentrum. The Checker will then query that Impfzentrum if there are any free appointments in general.

For each `dataset`entry without Vermittlungscode, you can optionally state an `age`. The checker will then check if a Vermittlungscode is available for the given age. If you don't state an `age`, the checker will search with `age = 99`.

##### Example:
```
{
   "name": "Impfzentrum Stuttgart Liederhalle",
   "plz": 70174,
   "url": "002-iz.impfterminservice.de",
   "age": 40,
   "notification_emails": ["erika@mustermann.de"]
}
```

#### Resources to build your `config.json`
* You can find all Impfzentrum URLs + PLZs here: https://www.impfterminservice.de/assets/static/impfzentren.json
* Enter your SMTP credentials to get notified via email.
* Create a Telegram Bot using the [Botfather](https://telegram.me/botfather) and enter your Chat ID(s) (use the [IDBot](https://telegram.me/myidbot) to get your ID) to get notified via Telegram.
* For documentation reasons - you can find the available Impfstoffe here: https://001-iz.impfterminservice.de/assets/static/its/vaccination-list.json
* Remark: If you get lots of "Unbekannter Fehler ist aufgetreten" (= HTTP 429 Too Many Requests), increase the waiting time between each run.

## Run
Run the Checker using
```
python3 impftermin_checker.py
```

Using the `selenium` framework, the Checker will then remote-control a Firefox to simulate a user checking for appointments and Vermittlungscodes.
If there's an appointment available, it will stop, play an alarm sound, leave the browser window open and notify you via email and Telegram.
If there's a Vermittlungscode, it will notify you via email and Telegram, but will not stop nor play an alarm sound.


## Open Todos
* ~Implement the search for an appointment without having a Vermittlungscode~
* Create an individual Telegram notification configuration for each `dataset` entry, instead of one global configuration (similar to email notifications).
* Refactor the `check_impftermin` function, probably also the `send_email` and `send_telegram_notification` functions, to class functions instead of global functions.
## Backlog ideas
* ~Send notifications via Telegram Bot~