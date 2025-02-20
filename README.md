# Syncsketch Python API

This package provides methods to communicate with the syncsketch servers and wraps CRUD (create, reade, update, delete) methods to interact with Reviews.

# Overview

SyncSketch is a syncronized visual review tool for the Film/TV/Games industry.

API access requires an enterprise level account.  For help or more info reach out to us.

### Getting Started

#### Compatibility
This library was tested with and confirmed on python versions:
- 2.7.14+
- 3.6
- 3.8

PyPi package

https://pypi.org/project/syncsketch/

#### Installation

Install using `pip`...

    pip install syncsketch


### Basic Examples


#### Authentication
Before we can start working, we need to get an `API_KEY` which you can obtain from the syncsketch [settings page](https://syncsketch.com/pro/#/userProfile/settings). Follow the given link, login and scroll down to the bottom headline `Developer Options` to see your 40 character API-Key.


Setting up a connection with your SyncSketch projects is as easy as following. 

    from syncsketch import SyncSketchAPI
    
    username = "username"
    api_key = "api-key-123"
    
    s = SyncSketchAPI(username, api_key)
    
    s.is_connected()

If you got a `200` response, you successfully connected to the syncsketch server! You can proceed with the next examples. We will skip the setup code for the next examples and the snippets will rely on each other, so make sure you run them one by one.


##### 1) Choose your account

Before we can create/read/update/delete reviews, we need to select an account

    accounts = s.get_accounts()
    first_account = accounts["objects"][0]

##### 2) Create a project

Let's create a project with the selected account

    project = s.create_project(first_account["id"], 'DEV Example Project', 'DEV API Testing')

This creates a new Project called `Dev Example Project` with the description `DEV API Testing`


##### 3) Create a review

We can now add a Review to our newly created Project using it's `id`

    review = s.create_review(project['id'], 'DEV Review (api)','DEV Syncsketch API Testing')


##### 4) Get list of reviews


    print(s.get_reviews_by_project_id(project['id'])


##### 5) Upload a review item

You can upload a file to the created review with the review id, we provided one example file in this repo under `examples/test.webm` for testing.

    item_data = s.add_media(review['id'],'examples/test.webm')


If all steps were successful, you should see the following in the web-app. 

![alt text](https://github.com/syncsketch/python-api/blob/documentation/examples/ressources/exampleResult.jpg?raw=true)

### Additional Examples

##### Adding a user to the project
    addedUsers = s.add_users_to_project(
        project_id,
        [
            {'email': 'test@syncsketch.com', 'permission':'viewer'}
        ],
        "This is a note to include with the welcome email"
    )
    print(addedUsers)


##### Update sort order of items in a review

    response = s.sort_review_items(
        review_id,
        [
            { "id": 111, "sortorder": 0 },
            { "id": 222, "sortorder": 1 },
            { "id": 333, "sortorder": 2 },
        ]
    )
    print(response)
    Out[1] {u'updated_items': 3}


##### Move items from one review to another

    response = s.move_items(
        new_review_id,
        [
            {
                "review_id": old_review_id,
                "item_id": item_id,
            }
        ]
    )


##### Traverse all Reviews
    projects = s.get_projects()
    
    for project in projects['objects']:
        print(project)


##### Traverse all Accounts 
The fastest way to traverse all Accounts, Projects Reviews, and Items is to get the entire tree

    tree_data = s.get_tree(withItems = True)
    
    for account in tree_data:
        for project in account['projects']:
            if project['active'] == 1:
                print project['name']
                for review in project['reviews']:
                    for item in review['items']:
                        mediaid = item['id']
                        medianame = item['name']
                        print '\t %s:\t%s'%(mediaid, medianame)
