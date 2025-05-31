# Tweet Manager MongoDB

This repository contains two Python scripts:

* **load-json.py**: Loads tweet data from a JSON file into a local MongoDB database.
* **Manager.py**: Provides an interactive command-line interface to search, list, and compose tweets in the same MongoDB database.

Use this README to:

1. Install prerequisites
2. Place your JSON file in the correct folder
3. Load tweets into MongoDB
4. Launch the Manager dashboard


## Prerequisites

1. **Python 3**
   Make sure Python 3 is installed and available on your system. 

2. **MongoDB Community Edition**

   * The `mongod` daemon must be installed and on your system’s PATH.

3. **Python Dependencies**
   Install the MongoDB driver for Python:


## Adding Your JSON File

1. **Locate the folder** where `load-json.py` and `Manager.py` reside (Inside`Code/`).
2. **Copy your JSON file** into that folder.

   * Example: If your filename is `tweets_sample.json`, place it alongside `load-json.py`.
   * Each line of the JSON file must be a valid JSON object representing a tweet.
   * Example of a single line in the JSON file:

     ```json
     {"url":"https://twitter.com/Example/status/123","date":"2021-01-01T00:00:00+00:00","content":"Hello world","user":{"username":"ExampleUser","displayname":"Example User","location":"Earth","followersCount":100},"retweetCount":5,"likeCount":10,"quoteCount":2}
     ```


## Step 1: Load JSON Data into MongoDB

1. **Open a terminal** and navigate to the folder containing `load-json.py`. For example:

   ```bash
   cd path/to/repository/Code
   ```

2. **Run the loader script** with two arguments:

   * The JSON filename (e.g. `tweets_sample.json`)
   * The MongoDB port number (e.g. `27017`)

   ```bash
   python3 load-json.py tweets_sample.json 27017
   ```

3. **Expected Output**

   ```
   Connected to MongoDB on port 27017
   Existing 'tweets' collection dropped.
   New 'tweets' collection created.
   Inserted the final batch of N tweets.
   All tweets have been inserted successfully!
   ```

   * This indicates that MongoDB automatically created (or replaced) a database named `"ProjectDB"` and stored your tweets under the `tweets` collection.
   * If you see a `FileNotFoundError` complaining about a directory name (e.g., `TweetManager-MongoDB`), open `load-json.py` and update the default folder name in the `find_assignment_directory()` function to match your local folder. Then re-run the loader.

4. **Verify in the Mongo Shell (optional)**

   ```bash
   mongo --port 27017
   ```

   In the Mongo prompt:

   ```
   use ProjectDB
   db.tweets.countDocuments()   // should print the number of tweets you loaded (N)
   ```

---

## Step 2: Launch the Manager Dashboard

1. **Open a terminal** (or reuse the existing one) and navigate to the folder containing `Manager.py`:

   ```bash
   cd path/to/repository/Code   # or repository root if scripts were not moved
   ```

2. **Run**:

   ```bash
   python3 Manager.py
   ```

3. **When prompted**, enter the same port number you used for `load-json.py` (e.g. `27017`):

   ```
   Enter MongoDB port: 27017
   ```

   You should see:

   ```
   Connected to MongoDB on port 27017
   ```

4. **The Main Menu**
   After connecting, the screen clears and you will see:

   ```
   Main Menu:
   1. Search for tweets
   2. Search for users
   3. List top tweets
   4. List top users
   5. Compose a tweet
   6. Exit
   Enter your choice:
   ```

   Select any option by typing the corresponding number and pressing Enter.

---

## Manager Menu Options

1. **Search for tweets**

   * Type `1` and press Enter.
   * You will be prompted:

     ```
     Enter keywords (space-separated) (or 0 to go back to main menu):
     ```
   * Enter one or more keywords (e.g. `FarmersProtest`).
   * The script will display a numbered list of matching tweets (ID, date, content, username).
   * After the list, you can enter a tweet number to view full metadata, or `0` to return to the Main Menu.

2. **Search for users**

   * Type `2` and press Enter.
   * You will be prompted:

     ```
     Enter the search term (or 0 to go back to main menu):
     ```
   * Enter a term to match against `user.displayname` or `user.location`.
   * The script displays usernames with matching displaynames/locations.
   * Enter a user number to view full user details, or `0` to return to the Main Menu.

3. **List top tweets**

   * Type `3` and press Enter.
   * You will be prompted:

     ```
     Enter the field to sort by (retweetCount/likeCount/quoteCount) (or 0 to go back to main menu):
     ```
   * Enter one of `retweetCount`, `likeCount`, or `quoteCount`.
   * Next you will see:

     ```
     Enter the number of top tweets to list:
     ```
   * Enter a positive integer (e.g. `5`).
   * The script lists the top N tweets sorted by the chosen field.
   * You are then asked if you want to see full details of any tweet.

4. **List top users**

   * Type `4` and press Enter.
   * You will be prompted:

     ```
     Enter the number of top users to list (or 0 to go back to main menu):
     ```
   * Enter a positive integer (e.g. `5`).
   * The script groups tweets by `user.username`, picks the maximum `followersCount` for each user, sorts descending, and displays the top N users (username, displayname, followers).
   * You can then select a user number to see full user details, or `0` to return to the Main Menu.

5. **Compose a tweet**

   * Type `5` and press Enter.
   * You will be prompted:

     ```
     Enter your tweet (or 0 to go back to main menu):
     ```
   * Type the content of a new tweet and press Enter.
   * The script inserts a new document into `ProjectDB.tweets` with the current timestamp and a hard-coded `user.username` of `"291user"`.
   * You should see: `Tweet composed successfully!`
   * The new tweet is now stored in MongoDB.

6. **Exit**

   * Type `6` and press Enter.
   * The script prints `Exiting…` and terminates.

---

## Example Workflow

1. **Place your JSON file** `tweets_sample.json` in the same folder as `load-json.py`.
2. **Load that JSON** into MongoDB:

   ```bash
   python3 load-json.py tweets_sample.json 27017
   ```
3. **Verify** (optional):

   ```bash
   mongo --port 27017
   > use ProjectDB
   > db.tweets.countDocuments()   // should match the number of lines in tweets_sample.json
   ```
4. **Run the Manager dashboard**:

   ```bash
   python3 Manager.py
   ```
5. **Enter** `27017` when prompted.
6. **Select menu options** (e.g. `3` → `retweetCount` → `1`) to list top tweets.

---

## Troubleshooting

* If you see **“No tweets found”** when listing top tweets:

  1. Confirm `load-json.py` was run with `DATABASE_NAME = "ProjectDB"`.
  2. Confirm `Manager.py` is using `DATABASE_NAME = "ProjectDB"` and `db = client[DATABASE_NAME]`.
  3. In the mongo shell, run:

     ```js
     use ProjectDB
     db.tweets.countDocuments()
     ```

     Make sure this returns a positive integer (the number of tweets you loaded).

* If you see a **FileNotFoundError** when running `load-json.py`, update the `find_assignment_directory()` function’s `dir_name` to match the name of your project folder.

* If the **port is in use**, choose another port (e.g. `28017`) when running both scripts.

---

## Notes

* MongoDB automatically creates the database (`ProjectDB`) and the collection (`tweets`) the first time you insert data. You do not need to pre-create them manually.
* All data is stored locally under a folder named `mongodb-data-<uuid>` created by `load-json.py`. The location of that folder does not affect the ability of `Manager.py` to read data.
* You can rename `DATABASE_NAME` in both scripts if you prefer a different database name. Just run `load-json.py` again after changing it.

