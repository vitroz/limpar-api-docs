---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - JSON

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Introduction

###Limpar API Docs

A guideline on how to perform requests on endpoints to retrieve all kinds of information.

# Authentication

> Input.


```shell
# With shell, you can just pass the correct header with each request
curl -X POST "api/v1/authenticate" -H \
'Content-Type: application/json' -d \
'{ "email": "user@mail.com", "password": "user-password" }'
```

> Output

```
{
    "token": "<token>"
}
```

> Error Output (Invalid credentials)

```
{
    "status": "401",
    "details": [
        {
            "message": "Incorrect Email or Password"
        }
    ]
}
```

> Error Output (Token not present on header)

```
{
    "status": "401",
    "details": [
        {
            "message": "Authorization Token is not present"
        }
    ]
}
```

To authorize, you need to generate a token by performing a `POST` request to 

the `/authenticate` endpoint, also sending along your user credentials in `JSON`format.

Then you can use the token on request header for all api requests, 

alongside the word `Bearer` by using `Authorization` key.


`Authorization: Bearer <token>`

<aside class="notice">
You must replace <code>token</code> with your personal API key.
</aside>

# Filtering

Filtering on the API is done by parameters provided in the URL in single format or multiple (array) 

Parameters are case insesitive, and can be combined.

By Default, the first filter is always preceded by a `?`, afterwards, they are preceded by a `&` sign.

### Available Filtering Options

Parameter  | Description
---------  | -----------
=  | Single Filter Option
[]=  | Multiple Filter Option, ex: `?param[]=1,&param[]=2` will result in `param[1,2]`.
...  | Range filter. Retrieves data that fit between two parameters. Note: the param must be defined as `mulitple_range` in the back end.  It can be used in tree distinct ways. ?param1...param2; ?...param2; ?param1...

> Example (Single) `GET /api/v1/endpoint?param=a`:

````json
[
    {
        "param": "a",
    },
]

```

> Example (Multiple) `GET /api/v1/endpoint?param[]=A&param[]=b`:

````json
[
    {
        "param": "a",
    },
    {
        "param": "b",
    },
]

```

# Sorting

Sorting on the API is done by using allowed columns specified on each endpoint/entity provided in the URL. By default the order returned is `DESC`, but it can be specified, by using `>` to represent and return in `ASC` or `<` to enforce `DESC` order.

### Available Sorting Options

Parameter  | Description
---------  | -----------
>  | retrieves data in ASC order.
<  | retrieves data in DESC order.

> Example  `GET /api/v1/endpoint?sorting_by=param`:

````json
[
    {
        "param": "b",
    },
    {
        "param": "a",
    },
]

```

> Example (ASC) `GET /api/v1/endpoint?sorting_by=>param`:

````json
[
    {
        "param": "a",
    },
    {
        "param": "b",
    },
]

```

# Pagination

Pagination on the API is done by using `page` and `per_page` options as URL parameters to control the number of items retrieved, and the set of items to be retrieved.

### Available Pagination Options

Parameter  | Description
---------  | -----------
page  | the number of page, defined by the total number of records to be returned considering the per_page option
per_page | the number of items to be retrieved - Default is set to 20.

> Example  `GET /api/v1/endpoint?page=1&per_page=5`:

````json
[
    {
        "param": "b",
    },
    {
        "param": "a",
    },
    {
        "param": "c",
    },
    {
        "param": "d",
    },
    {
        "param": "e",
    },
]

```

# Cache

Caching is used across the API for all '/' `index` requests, to improve response time on subsequent requests.

It is also used on authentication tokens, to keep it alive for 2 hours, and renew it everytime it is used during this time window.

Cache for requests has a default duration of 10 minutes, and it expires and refreshs automatically next time resource is requested.

### Skipping Cache

Parameter  | Description
---------  | -----------
fresh  | can be used as a query parameter to skip cache and return current response.

> Example  `GET /api/v1/endpoint?page=1&per_page=5&fresh`:

````json
[
    {
        "param": "a",
    },
    {
        "param": "b",
    },
    {
        "param": "c",
    },
    {
        "param": "d",
    },
    {
        "param": "e",
    },
]

```

# Users

### User Fields

Fields | Type | Additional Info
--------- | ------- | -----------
first_name | String | -
last_name | String | -
password_digest | String | holds the user password, in encrypted form.
email | String | *Unique* 


## Creates a User

This endpoint creates a user. It is protected by a `Create` action to the subject `User` present 
by default on role `Admin`.

`POST /api/v1/users`

<aside class="success">
If the request is successful, the API will return a 200 (success) status code
</aside>

> Input.


```json
# You must pass the correct authorization header header with each request

curl -X POST "api/v1/users" -H \
'Content-Type: application/json' -d \
'{
 "user" : 
    {
    "email": "user@mail.com", 
    "password": "sample-pwd",
    "first_name": "User",
    "last_name": "Last Name"
    }
} '
```


## Get All Users

```

```

> The above command returns JSON structured like this:

```json
[
    {
        "id": "ca15e0b7-7c83-42e0-8ba2-655904293fb9",
        "first_name": "A-User",
        "last_name": "Admin",
        "email": "user-admin@user.com"
    },
    {
        "id": "cdea3e6f-07c4-4d67-882f-4f082a75942d",
        "first_name": "Reg User",
        "last_name": "Last name",
        "email": "user-reg@mail.com"
    }
]
```
> Users alongside roles:

```json
[
    {
        "id": "ca15e0b7-7c83-42e0-8ba2-655904293fb9",
        "first_name": "A-User",
        "last_name": "Admin",
        "email": "user-admin@user.com",
        "roles": [
            {
                "id": "46497b96-416c-47ca-adc5-c52ffbd9360a",
                "name": "Admin"
            }
        ]
    },
    {
        "id": "cdea3e6f-07c4-4d67-882f-4f082a75942d",
        "first_name": "Reg User",
        "last_name": "Last name",
        "email": "user-reg@mail.com",
        "roles": [
            {}
        ]
    }
]
```

This endpoint retrieves all users. It is protected by a `List` action to the subject `User` present 
by default on role `Admin`.

### HTTP Request

`GET /api/v1/users`

### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve users under specific id.
email  | retrieves an user by email addresses.

### Includable Resources

`GET /api/v1/users?include[]=roles`

Parameter  | Description
---------  | -----------
roles | retrieve users alongside permission role(s) associated.

### Sortable Columns

`GET /api/v1/users?sorting_by[]=<param>`


Parameter | Default | Description
--------- | ------- | -----------
last_name | DESC | retrieve users sorted by last name.
email | DESC | retrieve users sorted by email.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific User


> The above command returns JSON structured like this:

```json
  {
      "id": "ca15e0b7-7c83-42e0-8ba2-655904293fb9",
      "first_name": "A-User",
      "last_name": "Admin",
      "email": "user-admin@user.com"
  }
```

This endpoint retrieves a specific user.

### HTTP Request

`GET api/v1/users/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the user to retrieve

## Update/Edit User Data

This endpoint is used to edit/update a specific user.

`PATCH /api/v1/users/<ID>`

> Input

```json
{
 "user" : 
    {
    "last_name": "Last Name Edited"
    }
} 
```


## Delete a Specific User


This endpoint deletes a specific user.

### HTTP Request

`DELETE api/v1/users/<ID>`

<aside class="notice">
The endpoint returns status 204 (No content) if entity is deleted.
</aside>


### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the user to delete

# Roles

A role defines a group of actions that an `User` can perform on the API. Roles are associated to `users` and `actions`

### Role Fields

Fields | Type | Additional Info
--------- | ------- | -----------
name | String | -

## Default Role Permissions

Role | Subject | Permissions
---------  | ----------- | -----------
Admin  | User | List, Create, Delete, Update, Show
User  | Rosters | List

# Actions

An action defines a permission that an `User` is allowed to perform on the API. Actions are associated to `roles`.

A set of action examples is `List`, `Create`, `Delete`, etc.

### Action Fields

Fields | Type | Additional Info
--------- | ------- | -----------
name | String | name of the action
subject | String | represents a entity to receive an action

# Organizations

### Organization Text Search

Searching on orgs using full text search. Endpoint used for searching orgs by partial name/email address values.

Example => `Organization: Company of Industrial Segment on Technology`

`GET /api/v1/search/organizations?name[]=Technology`

`GET /api/v1/search/organizations?name[]=Industrial`

Using this endpoint, complex text searches can be performed to retrieve data.

## Search Text params 

Parameters | Description
---------  | ----------- 
name  | -
email  | -


`GET /api/v1/search/organizations`


### Organization Fields

Fields | Type | Additional Info
--------- | ------- | -----------
name | String | -
name_alias | String |-
email | String |-
foia_email | String |-
phone | String |-
web | String |-
facebook | String |-
twitter | String |-
linkedin | String |-
keywords | String |-
ip | Integer |-
description | String |-
description_source | String |-
category_id | String | (UUID) represents an organization category.
primary_lat | Decimal |-
primary_lon | Decimal |-
alias_lat | Decimal |-
alias_lon | Decimal |-

## Get All Organizations

It returns all organizations

```

```

> The above command returns JSON structured like this:

```json
[
    {
        "id": "4a43f351-7b62-42f2-9b32-9832465d271f",
        "name": "Sample Org",
        "email": "sample-org@mail.com",
        "foia_email": null,
        "phone": "131313133131",
        "keywords": null,
        "ip": null,
        "description": null,
        "category_id": "60f0bde5-77b3-4e19-9f82-ee4128396bfa",
        "primary_lat": null,
        "primary_lon": null
    },
    {
        "id": "1fc18c05-da40-4607-a901-3d78c523cea6",
        "name": "Sample Org 2",
        "email": "sample-org-2@mail.com",
        "foia_email": null,
        "phone": "212121212121",
        "keywords": null,
        "ip": null,
        "description": null,
        "category_id": "8bf4e6a3-a800-4538-a38a-e305069999ba",
        "primary_lat": null,
        "primary_lon": null
    }
]
```
> Organization alongside categories:

```json
[
    {
        "id": "4a43f351-7b62-42f2-9b32-9832465d271f",
        "name": "Walker, Gary (Mr.) .",
        "email": "walkergrym@mail.com",
        "foia_email": null,
        "phone": "8764883990",
        "keywords": null,
        "ip": null,
        "description": null,
        "category_id": "60f0bde5-77b3-4e19-9f82-ee4128396bfa",
        "primary_lat": null,
        "primary_lon": null,
        "category": [
            {
                "id": "60f0bde5-77b3-4e19-9f82-ee4128396bfa",
                "name": "Sample Category",
                "type_id": "4f178839-54c5-42de-a0cf-dc095d43e70c"
            }
        ]
    },
]
```

### HTTP Request

`GET /api/v1/organizations`

### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve organization under specific id.
name  | retrieves an organization by name.
email  | retrieves an organization by email addresses.
category_id  | retrieves an organization by category_id.

### Includable Resources

`GET /api/v1/organizations?include[]=category`

Parameter  | Description
---------  | -----------
category | retrieve organizations alongside category associated.

### Sortable Columns

`GET /api/v1/organizations?sorting_by[]=<param>`


Parameter | Default | Description
--------- | ------- | -----------
name | DESC | retrieve organizations sorted by name.
created_at | DESC | retrieve organizations sorted by created at data.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Organization

> The above command returns JSON structured like this:

```json
  {
      "id": "4a43f351-7b62-42f2-9b32-9832465d271f",
      "name": "Sample Org",
      "email": "sample-org@mail.com",
      "foia_email": null,
      "phone": "131313133131",
      "keywords": null,
      "ip": null,
      "description": null,
      "category_id": "60f0bde5-77b3-4e19-9f82-ee4128396bfa",
      "primary_lat": null,
      "primary_lon": null
  }
```

This endpoint retrieves a specific organization.

### HTTP Request

`GET api/v1/organizations/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the organization to retrieve

# Organization Categories

### Organization Category Fields

Fields | Type | Additional Info
--------- | ------- | -----------
name | String | -
type_id | String | (UUID) represents an organization type.

## Get All Organization Categories

It returns all organization categories


> The above command returns JSON structured like this:

```json
[
    {
        "id": "8bf4e6a3-a800-4538-a38a-e305069999ba",
        "name": "Category Sample",
        "type_id": "65656b1f-ae44-4327-b9cf-a8629354a688"
    },
    {
        "id": "60f0bde5-77b3-4e19-9f82-ee4128396bfa",
        "name": "Category Sample 2",
        "type_id": "4f178839-54c5-42de-a0cf-dc095d43e70c"
    }
]
```
> Organization categories alongside types:

```json
[
    {
        "id": "8bf4e6a3-a800-4538-a38a-e305069999ba",
        "name": "Category Sample 1",
        "type_id": "65656b1f-ae44-4327-b9cf-a8629354a688",
        "type": [
            {
                "id": "65656b1f-ae44-4327-b9cf-a8629354a688",
                "name": "Type 1"
            }
        ]
    },
]
```

### HTTP Request

`GET /api/v1/organization_categories`

### Includable Resources

`GET /api/v1/organization_categories?include[]=type`

Parameter  | Description
---------  | -----------
type | retrieve organization categories alongside type associated.

<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Organization Category

> The above command returns JSON structured like this:

```json
  {
      "id": "8bf4e6a3-a800-4538-a38a-e305069999ba",
      "name": "Category Sample 1",
      "type_id": "65656b1f-ae44-4327-b9cf-a8629354a688",
  },
```

This endpoint retrieves a specific organization category.

### HTTP Request

`GET api/v1/organization_categories/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the organization category to retrieve


# Organization Types

### Organization Type Fields

Fields | Type | Additional Info
--------- | ------- | -----------
name | String | -


<aside class="notice">
Types can be retrieved though organization categories
</aside>

> Organization categories alongside types:

```json
[
    {
        "id": "8bf4e6a3-a800-4538-a38a-e305069999ba",
        "name": "Category Sample 1",
        "type_id": "65656b1f-ae44-4327-b9cf-a8629354a688",
        "type": [
            {
                "id": "65656b1f-ae44-4327-b9cf-a8629354a688",
                "name": "Type 1"
            }
        ]
    },
]
```

# Athletics

This section lists athletics data points accessible through other endpoints as includable resources.

### Associations

Association | Additional Info
--------- |  -----------
Athletic Level |  Represents a team school level (university, high school, etc).
Athletic Association | represents an athletic association tied to a team.
Athletic Conference | represents an athletic conference tied to team.
Athletic Sport | represents a sport, accessible through a team.

# Teams

### Team Fields

Fields | Type | Additional Info
--------- | ------- | -----------
name | String | -
sex | String |-
athletic_level_id | String | (UUID) represents an athletic level.
athletic_association_id | String | (UUID) represents an athletic association.
athletic_conference_id | String | (UUID) represents an athletic conference.
athletic_sport_id | String | (UUID) represents a sport.


## Get All Teams

It returns all teams

```

```

> The above command returns JSON structured like this:

```json
[
    {
        "id": "018131db-9d54-4324-a116-154712d6822e",
        "name": "Team",
        "sex": "male"
    },
    {
        "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "name": "Team 2",
        "sex": "male"
    }
]
```
> Team alongside sport:

```json
[
    {
        "id": "018131db-9d54-4324-a116-154712d6822e",
        "name": "Team 1",
        "sex": "male",
        "sport": [
            {
                "id": "2a364c28-e46a-44aa-b1ba-11ab02279d6e",
                "name": "Soccer"
            }
        ]
    },
    {
        "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "name": "Team 2",
        "sex": "male",
        "sport": [
            {
                "id": "2a364c28-e46a-44aa-b1ba-11ab02279d6e",
                "name": "Soccer"
            }
        ]
    }
]
```

> Team alongside sport and association:

```json
[
    {
        "id": "018131db-9d54-4324-a116-154712d6822e",
        "name": "Team",
        "sex": "male",
        "sport": [
            {
                "id": "2a364c28-e46a-44aa-b1ba-11ab02279d6e",
                "name": "Soccer"
            }
        ],
        "athletic_association": [
            {
                "id": "5bc9af39-058f-4bba-a8d8-c83ee59399f9",
                "name": "Association First"
            }
        ]
    }
]
```

### HTTP Request

`GET /api/v1/teams`

### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve team under specific id.
name  | retrieves an team by name.
sex  | retrieves an team by sex.

### Includable Resources

`GET /api/v1/teams?include[]=sport`

`GET /api/v1/teams?include[]=sport&include[]=athletic_association`

Parameter  | Description
---------  | -----------
sport | retrieve organizations alongside sport associated.
athletic_association | retrieve organizations alongside athletic association associated.
level | retrieve organizations alongside school level associated.
conference | retrieve organizations alongside conference associated.


### Sortable Columns

`GET /api/v1/teams?sorting_by[]=<param>`

Parameter | Default | Description
--------- | ------- | -----------
name | DESC | retrieve teams sorted by name.
created_at | DESC | retrieve teams sorted by created at data.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Team

> The above command returns JSON structured like this:

```json
    {
        "id": "018131db-9d54-4324-a116-154712d6822e",
        "name": "Team",
        "sex": "male"
    },
```

This endpoint retrieves a specific team.

### HTTP Request

`GET api/v1/teams/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the team to retrieve

# Games (Schedules)

### Game Fields

Fields | Type | Additional Info
--------- | ------- | -----------
athletic_season_id | String | (UUID) represents a season.
home_team_id | String | (UUID) represents a team.
away_team_id | String | (UUID) represents a team.
venue | String | -
home | Boolean | A game that has occured at its foundation place indicator
data_time | Date | The time of the match


## Get All Games

It returns all games

### HTTP Request

`GET /api/v1/games`

> The above command returns JSON structured like this:

```json
[
    {
        "id": "7ccf336c-97e4-449c-8408-d170e95264a9",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "away_team_id": "018131db-9d54-4324-a116-154712d6822e",
        "venue": "Venue",
        "date_time": "2021-04-05T11:46:15.793Z"
    },
    {
        "id": "9e7278f4-a99c-4923-9f02-635a662a65c7",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "bfb6b160-33b8-4215-8980-063058491fca",
        "away_team_id": "15268441-855b-42bc-a8b1-71ca11c4fc92",
        "venue": "Venue 2",
        "date_time": "2021-04-05T11:47:18.474Z"
    }
]
```
> Game alongside teams involved:

```json
[
    {
        "id": "7ccf336c-97e4-449c-8408-d170e95264a9",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "away_team_id": "018131db-9d54-4324-a116-154712d6822e",
        "venue": "Venue 1",
        "date_time": "2021-04-05T11:46:15.793Z",
        "home_team": [
            {
                "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
                "name": "Team 1",
                "sex": "male"
            }
        ],
        "away_team": [
            {
                "id": "018131db-9d54-4324-a116-154712d6822e",
                "name": "Team 2",
                "sex": "male"
            }
        ]
    },
    {
        "id": "9e7278f4-a99c-4923-9f02-635a662a65c7",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "bfb6b160-33b8-4215-8980-063058491fca",
        "away_team_id": "15268441-855b-42bc-a8b1-71ca11c4fc92",
        "venue": "Venue 1",
        "date_time": "2021-04-05T11:47:18.474Z",
        "home_team": [
            {
                "id": "bfb6b160-33b8-4215-8980-063058491fca",
                "name": "Team 3",
                "sex": "male"
            }
        ],
        "away_team": [
            {
                "id": "15268441-855b-42bc-a8b1-71ca11c4fc92",
                "name": "Team 4",
                "sex": "male"
            }
        ]
    }
]
```

`GET /api/v1/games?season=2015...2020`

> Games that fit between season start 2015 and season end 2020

```json
[
    {
        "id": "7ccf336c-97e4-449c-8408-d170e95264a9",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "away_team_id": "018131db-9d54-4324-a116-154712d6822e",
        "venue": "Venue 1",
        "date_time": "2021-04-05T11:46:15.793Z"
    },
    {
        "id": "1aa7d5d5-47c0-4b7f-aa40-f894faff2753",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b9386ac4-ccb9-4532-8c4e-b2e7551c5d07",
        "away_team_id": "6295c59d-6cc0-4161-832e-a398185f147c",
        "venue": "Venue 2",
        "date_time": "2021-04-05T11:47:49.067Z"
    }
]
```

`GET /api/v1/games?season=2015...`

> Games that fit between season start 2015 and foward

```json
[
    {
        "id": "7ccf336c-97e4-449c-8408-d170e95264a9",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "away_team_id": "018131db-9d54-4324-a116-154712d6822e",
        "venue": "Venue 1",
        "date_time": "2021-04-05T11:46:15.793Z"
    },
    {
        "id": "1aa7d5d5-47c0-4b7f-aa40-f894faff2753",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b9386ac4-ccb9-4532-8c4e-b2e7551c5d07",
        "away_team_id": "6295c59d-6cc0-4161-832e-a398185f147c",
        "venue": "Venue 2",
        "date_time": "2021-04-05T11:47:49.067Z"
    }
    {
        "id": "1aa7d5d5-47c0-4b7f-aa40-f894faff2753",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b9386ac4-ccb9-4532-8c4e-b2e7551c5d07",
        "away_team_id": "6295c59d-6cc0-4161-832e-a398185f147c",
        "venue": "Venue 3",
        "date_time": "2021-04-05T11:47:49.067Z"
    }
]
```

`GET /api/v1/games?season=...2020`

> Games that fit between season end 2020 and below

```json
[
    {
        "id": "7ccf336c-97e4-449c-8408-d170e95264a9",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "away_team_id": "018131db-9d54-4324-a116-154712d6822e",
        "venue": "Venue 1",
        "date_time": "2021-04-05T11:46:15.793Z"
    },
    {
        "id": "1aa7d5d5-47c0-4b7f-aa40-f894faff2753",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b9386ac4-ccb9-4532-8c4e-b2e7551c5d07",
        "away_team_id": "6295c59d-6cc0-4161-832e-a398185f147c",
        "venue": "Venue 2",
        "date_time": "2021-04-05T11:47:49.067Z"
    }
    {
        "id": "1aa7d5d5-47c0-4b7f-aa40-f894faff2753",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b9386ac4-ccb9-4532-8c4e-b2e7551c5d07",
        "away_team_id": "6295c59d-6cc0-4161-832e-a398185f147c",
        "venue": "Venue 3",
        "date_time": "2021-04-05T11:47:49.067Z"
    }
]
```


### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve game under specific id.
season_start  | retrieves a game by season_start.
season_end  | retrieves a game by season_end.
home_team  | retrieves a game by home team.
home_away  | retrieves a game by away team.
venue  | retrieves a game by the venue.
season  | retrieves a game between two dates (years).

### Includable Resources

`GET /api/v1/games?include[]=home_team&include[]=away_team`

Parameter  | Description
---------  | -----------
home_team | retrieve games alongside home team associated.
away_team | retrieve games alongside away team associated.

### Sortable Columns

`GET /api/v1/games?sorting_by[]=<param>`

Parameter | Default | Description
--------- | ------- | -----------
created_at | DESC | retrieve games sorted by record creation date.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Game

> The above command returns JSON structured like this:

```json
    {
        "id": "7ccf336c-97e4-449c-8408-d170e95264a9",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "home_team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "away_team_id": "018131db-9d54-4324-a116-154712d6822e",
        "venue": "Venue",
        "date_time": "2021-04-05T11:46:15.793Z"
    },
```

This endpoint retrieves a specific tegameam.

### HTTP Request

`GET api/v1/games/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the game to retrieve

# Rosters

### Roster Fields

Fields | Type | Additional Info
--------- | ------- | -----------
athletic_season_id | String | (UUID) represents an athletic season
first_name | String |-
last_name | String |-
positiion | String |-
feet | Integer |-
inches | Integer |-
weight | Integer |-
athlete_class | String |-
redshirt | Boolean |-
hometown | String |-
high_school | String |-
jersey_number | String |-
external_id | String |-
external_source | String |-
team_id | String | (UUID) represents a team.


## Get All Rosters

It returns all rosters

### HTTP Request

`GET /api/v1/rosters`

> The above command returns JSON structured like this:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "first_name": "Player",
        "last_name": "Player Last",
        "position": "Striker",
        "feet": 6,
        "inches": 6,
        "weight": 178,
        "athlete_class": "SR",
        "redshirt": null,
        "hometown": "Town",
        "high_school": "Some Highschool",
        "jersey_number": 9,
        "team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9"
    },
]
```
> Roster alongside teams involved:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "first_name": "Player",
        "last_name": "Player Last",
        "position": "Striker",
        "feet": 6,
        "inches": 6,
        "weight": 178,
        "athlete_class": "SR",
        "redshirt": null,
        "hometown": "Town",
        "high_school": "Some Highschool",
        "jersey_number": 9,
        "team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "team": [
            {
                "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
                "name": "Team",
                "sex": "male"
            }
        ]
    },
]
```

`GET /api/v1/rosters?season=2015...2020`

> Rosters that fit between season start 2015 and season end 2020

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "first_name": "Player",
        "last_name": "Last",
        "position": "Striker",
        "feet": 6,
        "inches": 6,
        "weight": 178,
        "athlete_class": "SR",
        "redshirt": null,
        "hometown": "Town",
        "high_school": "Some Highschool",
        "jersey_number": 9,
        "team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9"
    },
    {
        "id": "4ceb6427-dbc2-4d9b-8ab7-52e4bc27f917",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "first_name": "Player 2",
        "last_name": "Last 2",
        "position": "Striker",
        "feet": 5,
        "inches": 7,
        "weight": 163,
        "athlete_class": "SR",
        "redshirt": null,
        "hometown": "Town 2",
        "high_school": "Some Highschool",
        "jersey_number": 7,
        "team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9"
    }
]
```



### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve roster under specific id.
season_start  | retrieves a roster by season_start.
season_end  | retrieves a roster by season_end.
first_name  | retrieves a roster by first_name.
last_name  | retrieves a roster by last_name.
team_id  | retrieves a roster by team id.
season  | retrieves a roster between two dates (years).

### Includable Resources

`GET /api/v1/rosters?include[]team`

Parameter  | Description
---------  | -----------
team | retrieve rosters alongside team associated.
season | retrieve rosters alongside season associated

### Sortable Columns

`GET /api/v1/rosters?sorting_by[]=<param>`

Parameter | Default | Description
--------- | ------- | -----------
name | DESC | retrieve rosters sorted by name.
first_name | DESC | retrieve rosters sorted by first name.
last_name | DESC | retrieve rosters sorted by last name.
created_at | DESC | retrieve rosters sorted by created at.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Roster

> The above command returns JSON structured like this:

```json
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_season_id": "d12ob132-649c-4e02-aef9-ae27ghSc65kh",
        "first_name": "Player",
        "last_name": "Last",
        "position": "Striker",
        "feet": 6,
        "inches": 6,
        "weight": 178,
        "athlete_class": "SR",
        "redshirt": null,
        "hometown": "Town",
        "high_school": "Some Highschool",
        "jersey_number": 9,
        "team_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9"
    }
```

This endpoint retrieves a specific roster.

### HTTP Request

`GET api/v1/rosters/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the roster to retrieve


# Sport Statistics

### Sport Statistics Fields

Fields | Type | Additional Info
--------- | ------- | -----------
athletic_sport_id | String | (UUID) represents a sport.
statistic_id | String | (UUID) represents a statistic record.


## Get All Sport Statistics

It returns all sport statistics

### HTTP Request

`GET /api/v1/sport_statistics`

> The above command returns JSON structured like this:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_sport_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "statistic_id": "c25ob180-123a-1f49-bol2-hi10f21c80p2"
    },
]
```
> sport statistics alongside sport involved:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_sport_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "statistic_id": "c25ob180-123a-1f49-bol2-hi10f21c80p2",
        "sport": [
            {
                "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
                "name": "Soccer"
            }
        ]
    },
]
```

### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve sport statistic under specific id.
athletic_sport_id  | retrieves sport statistic by athletic_sport_id.
statistic_id  | retrieves sport statistic by statistic_id.

### Includable Resources

`GET /api/v1/sport_statistics?include[]=sport&include[]=statistic`

Parameter  | Description
---------  | -----------
sport | retrieve sport statistics alongside sport associated.
statistic | retrieve sport statistics alongside statistic associated.

### Sortable Columns

`GET /api/v1/sport_statistics?sorting_by[]=<param>`

Parameter | Default | Description
--------- | ------- | -----------
created_at | DESC | retrieve rosters sorted by created at.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Sport Statistic

> The above command returns JSON structured like this:

```json
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_sport_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "statistic_id": "c25ob180-123a-1f49-bol2-hi10f21c80p2"
    },
```

This endpoint retrieves a specific sport statistic.

### HTTP Request

`GET api/v1/sport_statistics/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the sport_statistics to retrieve


# Box Scores

### Box Scores Fields

Fields | Type | Additional Info
--------- | ------- | -----------
value | Integer | -
athletic_roster_id | String | (UUID) represents a roster.
statistic_id | String | (UUID) represents a statistic record.
game_id | String | (UUID) represents a game record.


## Get All Box Scores

It returns all box scores

### HTTP Request

`GET /api/v1/box_scores`

> The above command returns JSON structured like this:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "value": 0,
        "athletic_sport_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "statistic_id": "c25ob180-123a-1f49-bol2-hi10f21c80p2",
        "game_id": "l43ob180-123a-1f49-bol2-hi10f21c80tr"

    },
]
```
> box score alongside statistic involved:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_roster_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "statistic_id": "c25ob180-123a-1f49-bol2-hi10f21c80p2",
        "game_id": "p15ob180-123a-1f49-bol2-hi10f21c80p2",
        "statistic": [
            {
                "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
                "description": "something",
                "abbreviation": "abbrev"
            }
        ]
    },
]
```

### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve box scores under specific id.
value  | retrieve box scores under specific value.

### Includable Resources

`GET /api/v1/box_scores?include[]=roster&include[]=statistic&include[]=game`

Parameter  | Description
---------  | -----------
roster | retrieve box scores alongside roster associated.
statistic | retrieve box scores alongside statistic associated.
game | retrieve box scores alongside game associated.

### Sortable Columns

`GET /api/v1/box_scores?sorting_by[]=<param>`

Parameter | Default | Description
--------- | ------- | -----------
created_at | DESC | retrieve rosters sorted by created at.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Box Score

> The above command returns JSON structured like this:

```json
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "value": "some",
        "athletic_roster_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "statistic_id": "c25ob180-123a-1f49-bol2-hi10f21c80p2",
        "game_id": "o14ob180-123a-1f49-bol2-hi10f21c90u3"
    },
```

This endpoint retrieves a specific box score.

### HTTP Request

`GET api/v1/box_scores/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the box score to retrieve



# Game Matches

### Game Matches Fields

Fields | Type | Additional Info
--------- | ------- | -----------
match | Integer | -
home_team_points | Integer | -
away_team_points | Integer | -
game_id | String | (UUID) represents a game record.


## Get All Game Matches

It returns all game matches

### HTTP Request

`GET /api/v1/game_matches`

> The above command returns JSON structured like this:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "match": 0,
        "home_team_points": 0,
        "away_team_points": 0,
        "game_id": "l43ob180-123a-1f49-bol2-hi10f21c80tr"

    },
]
```
> game match alongside game involved:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "athletic_roster_id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
        "statistic_id": "c25ob180-123a-1f49-bol2-hi10f21c80p2",
        "game_id": "p15ob180-123a-1f49-bol2-hi10f21c80p2",
        "game": [
            {
                "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
                "venue": "something"
                .
                .
                .
            }
        ]
    },
]
```

### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve box scores under specific id.
match  | retrieve box scores under specific match.
home_team_points  | retrieve box scores under specific home_team_points.
away_team_points  | retrieve box scores under specific away_team_points.

### Includable Resources

`GET /api/v1/game_matches?include[]=game&include[]=sport_match`

Parameter  | Description
---------  | -----------
sport_match | retrieve box scores alongside sport match associated.
game | retrieve box scores alongside game associated.

### Sortable Columns

`GET /api/v1/game_matches?sorting_by[]=<param>`

Parameter | Default | Description
--------- | ------- | -----------
created_at | DESC | retrieve game matches sorted by created at.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Game Match

> The above command returns JSON structured like this:

```json
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "match": 0,
        "home_team_points": 0,
        "away_team_points": 0,
        "game_id": "l43ob180-123a-1f49-bol2-hi10f21c80tr"
    },
```

This endpoint retrieves a specific game match.

### HTTP Request

`GET api/v1/game_matches/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the box score to retrieve



# Sport Matches

### Sport Matches Fields

Fields | Type | Additional Info
--------- | ------- | -----------
label | String | -
athletic_sport_id | String | (UUID) represents a sport record.


## Get All Sport Matches

It returns all sport matches

### HTTP Request

`GET /api/v1/sport_matches`

> The above command returns JSON structured like this:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "label": "lbl",
        "athletic_sport_id": "l43ob180-123a-1f49-bol2-hi10f21c80tr"

    },
]
```
> sport match alongside sport involved:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "label": "lbl",
        "athletic_sport_id": "p15ob180-123a-1f49-bol2-hi10f21c80p2",
        "sport": [
            {
                "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
                "name": "soccer"
            }
        ]
    },
]
```

### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve sport match under specific id.
match  | retrieve sport match under specific match.
athletic_sport_id  | retrieve sport match under specific athletic_sport_id.


### Includable Resources

`GET /api/v1/sport_matches?include[]=sport`

Parameter  | Description
---------  | -----------
sport | retrieve sport_matches alongside sport associated.

### Sortable Columns

`GET /api/v1/sport_matches?sorting_by[]=<param>`

Parameter | Default | Description
--------- | ------- | -----------
created_at | DESC | retrieve game matches sorted by created at.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Sport Match

> The above command returns JSON structured like this:

```json
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "match": "lbl",
        "athletic_sport_id": "l43ob180-123a-1f49-bol2-hi10f21c80tr"

    },
```

This endpoint retrieves a specific game match.

### HTTP Request

`GET api/v1/sport_matches/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the box score to retrieve


# Game Scores

### Sport Game Score Fields

Fields | Type | Additional Info
--------- | ------- | -----------
label | String | -
athletic_sport_id | String | (UUID) represents a sport record.


## Get All Sport Game Scores

It returns all sport game scores

### HTTP Request

`GET /api/v1/sport_game_scores`

> The above command returns JSON structured like this:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "label": "lbl",
        "athletic_sport_id": "l43ob180-123a-1f49-bol2-hi10f21c80tr"
    },
]
```
> sport game scores alongside sport involved:

```json
[
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "label": "lbl",
        "athletic_sport_id": "p15ob180-123a-1f49-bol2-hi10f21c80p2",
        "sport": [
            {
                "id": "b95ba865-649c-4e02-aef9-ae26b30c82c9",
                "name": "soccer"
            }
        ]
    },
]
```

### Available Filters

Parameter  | Description
---------  | -----------
id  | retrieve sport game score under specific id.
label  | retrieve sport game score under specific label.
athletic_sport_id  | retrieve sport game score under specific athletic_sport_id.


### Includable Resources

`GET /api/v1/sport_game_scores?include[]=sport`

Parameter  | Description
---------  | -----------
sport | retrieve sport_game_scores alongside sport associated.

### Sortable Columns

`GET /api/v1/sport_game_scores?sorting_by[]=<param>`

Parameter | Default | Description
--------- | ------- | -----------
created_at | DESC | retrieve sport game score sorted by created at.


<aside class="success">
Remember ??? To use the token on request header, to be able to perform requests!
</aside>

## Get a Specific Sport Game Score

> The above command returns JSON structured like this:

```json
    {
        "id": "2da30e35-ff52-47c5-9517-628d1bccc968",
        "label": "lbl",
        "athletic_sport_id": "l43ob180-123a-1f49-bol2-hi10f21c80tr"
    },
```

This endpoint retrieves a specific game match.

### HTTP Request

`GET api/v1/sport_game_scores/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the sport game score to retrieve
