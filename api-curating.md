# Curation API documentation

Bloom's Curation API is available for third-party services and applications to easily integrate local news into their local user experiences. Registered local news publishers and business partners on Bloom use the API to set specific preferences for retrieving stories by location from verified local newsrooms. 

Use the following sections to learn how to use the API. If you have questions or run into an issue, please contact Stephen at steve@bloom.li.
<br><br>

### Table of Contents

1. [Configuration](#configuration)
2. [Authentication](#authentication)
3. [Methods](#methods)
    1. [Get Posts](#get-posts)
4. [Method Response](#method-response)
5. [Examples](#examples)
    1. [Get Post](#example-get-posts)
    2. [Successful Method Response](#example-successful-method-response)
    3. [Failed Method Response](#example-failed-method-response)

<br><br>

---

<br><br>

## Configuration

The Curation API is organized with <a href="http://en.wikipedia.org/wiki/Representational_State_Transfer" title="REST" target="_blank">REST</a>.

All API methods use the URL Endpoint: [https://api.bloom.li/post/](https://api.bloom.li/post/) that must be accessed via HTTPS. Methods are only accessible using GET requests. The default quota is 1 call per 5 seconds, per publisher.

<br><br>

---

<br><br>

## Authentication

Each method call *must* include the following fields in order to authenticate the request. Additional requirements are listed within each method's definition below.

| Attribute       | Type        | Value Description                                    |
| --------------- | ----------- | ---------------------------------------------------- |
| app_key         | String      | Application key                                      |
| app_action      | String      | Method slug. Provided in each method definition below|

<br>

### Additional Authentication Instructions

#### Application Key
Follow these instructions to obtain these keys:
1. Login to your Bloom account at <a href="https://www.bloom.li" title="Bloom Labs" target="_blank">bloom.li</a>
2. Go to your Account admin at <a href="https://www.bloom.li/account" title="Bloom Labs account" target="_blank">bloom.li/account</a>
3. If you registered as a Publisher, find the main <a href="https://www.bloom.li/account/publishers/settings" title="Publisher settings" target="_blank">Settings page</a> in the left-hand menu under Publishers
4. If you registered as a Business Partner, find the main <a href="https://www.bloom.li/account/businesses/settings" title="Business settings" target="_blank">Settings page</a> in the left-hand menu under Businesses
5. Find your account's private Application Key listed on this page

<br><br>

---

<br><br>

## Methods

The following method can be used to perform [Get](#get-posts) actions for curating news stories ("Post"). The method is explained below with the input that's required or optional for a single call.

After the call, a [Response](#method-response) will be returned with information about the request's result.

<br>

### Get Posts
This method will retrieve Posts. [View example](#example-get-posts).

*Requires attributes from [Authentication](#authentication).*
*Dot (.) represents array or object index.*


| Attribute       | Type        | Value Description                                |
| --------------- | ----------- | ------------------------------------------------ |
| app_action      | String      | "post_get"                                       |
| filter.location                        | Array      | Filter content by location  |
| filter.location.latitude               | Float      | Latitude coordinate of location epicenter  |
| filter.location.longitude              | Float      | Longitude coordinate of location epicenter  |
| filter.location.radius                 | Number     | Radius (in miles) from location epicenter  |
| filter.location.secondary              | Boolean    | Whether to retrieve Posts whose primary *or secondary* locations are within the filtered location. When geotagging, publishers have the option to add up to 29 secondary locations for each Post  |
| filter.location.get_surrounding        | Boolean    | Whether to retrieve Posts in the location's immediate geographic surroundings if no posts are found within the filtered location. Bloom will incrementally extend the radius to a maximum of 6 miles and retry  |
| filter.date                            | Array      | Filter content by date range or number of days in the future   |
| filter.date.start                      | Datetime   | Start of date range   |
| filter.date.end                        | Datetime   | End of date range   |
| filter.date.days                       | Number     | Days from current date   |


<br><br>

---

<br><br>

## Method Response

Upon a method call, the following data will be returned as a JSON object. Responses should first be checked by their *success* and *code* attributes. If either of those show the method call failed, the *code* or *message* should be used to identify the issue. The *data* attribute/object may not be available for unsuccessful method calls.

 View examples of [Successful Call](#example-successful-method-response) and [Failed Call](#example-failed-method-response).

| Attribute                              | Type       | Value Description            |
| -------------------------------------- | ------------------------------------------------ | --------------- |
| success                                | Boolean    | Whether or not a connection was made. If false, it is likely due to an error with the integration or internet connection. |
| code                                   | Number     | The type of response. See [Response Codes](#response-codes) section. If is unsuccessful code (anything other than "1"), it is likely due to an input error. |
| message                                | String     | A message describing the method's response code. |
| data                                   | Array      | Data about the content that was curated. |
| data.posts                             | Array      | Array of Post data |
| data.posts.post                        | Object     | The [Post object](#post-response-object)  |
| data.posts.post.location               | Object     | The Post's primary [location object](#location-response-object)  |
| data.posts.post.locations              | Array      | Array of secondary [location objects](#location-response-object). Provided if "secondary" parameter is true  |
| data.posts.post.publisher              | Object     | The Post's [publisher object](#publisher-response-object)  |
| data.posts.post.type.pt_name           | String     | The name of the Post Type; Options: "News" or "Event"  |
| data.posts.post.type.pt_slug           | String     | The slug of the Post Type name; Options: "news" or "event"  |

<br><br>

### Response Codes

Each method call returns a *code* that represents a specific type of successful or failed response.

- **1:** Method call completed without any error.
- **2:** Method call failed because the following required field(s) were not provided: *(List of fields)*
- **3:** Invalid Application Key provided in *app_key* field
- **4:** Inactive Application Key provided. API access is currently not active for this account.


<br><br>

### Post Response Object

The Post Response Object is included in the response from a [Method](#methods) call.

| Attribute                              | Name                 | Value Description            |
| -------------------------------------- | ------------------------------------------------ | --------------- |
| po_title                               | Title                | The title of the Post  |
| po_slug                                | Slug                 | The simplified slug version of the Post's title  |
| po_content                             | Content Excerpt      |  An excerpt of the Post as given by the Publisher. May be up to 1,000 charaters |
| po_url_raw                             | Post URL             | The URL of the Post that goes to the Publisher's website  |
| po_url_bloom                           | Map URL              | The URL of the Post that goes to the map on Bloom's website |
| po_image                               | Image URL            | The URL of the Post's image, if provided |
| po_publish_time                        | Published Date       | The datetime string of when the Post was published by the Publisher; EST Timezone |
| po_created_time                        | Geotagged Date       | The datetime string of when the Post was geotagged on Bloom; EST Timezone |
| is_surrounding                         | Surrounding          | A boolean value on whether the Post's primary location is surrounding the requested location, if "get_surrounding" parameter is provided. |

<br><br>

 
 
### Publisher Response Object

The Publisher Response Object is included in the response from a [Method](#methods) call.

| Attribute                              | Name                 | Value Description            |
| -------------------------------------- | ------------------------------------------------ | --------------- |
| pub_name                               | Name                 | The name of the Publisher  |
| pub_username                           | Username             | The simplified version of the Publisher's name  |
| pub_website_url                        | Website URL          | A simplified version of the Publisher's website URL |
| pub_website_url_raw                    | Website URL Full     | The full version of the Publisher's website URL  |
| pub_color                              | Branding Color       | The six-digit hexidecimal code of the Publisher's main branding color |
| pub_image                              | Image URL            | The image URL of the Publisher's logo |
| pub_map_url                            | Map URL              | The URL to an interactive map of news stories on the Publisher's website |


<br><br>


### Location Response Object

The Location Response Object is included in the response from a [Method](#methods) call. Depending on the location geotagged, not all attributes below will be included.

| Attribute                              | Name                 | Value Description            |
| -------------------------------------- | ------------------------------------------------ | --------------- |
| label                                  | Location Name        | The name given to this location, if provided by the publisher  |
| distance_k                             | Distance (km)        | The calculated distance (in kilometers) from the requested location |
| distance_m                             | Distance (mi)        | The calculated distance (in miles) from the requested location |
| loc_formatted_address                      | Formatted Address    | Always included in the Location Object. A human-readable address of this location. It is generally composed of one or more the components below. |
| loc_level                                  | Level                | The geographic depth of the location. Value is equal to one of the attributes below that best describe the depth.  |
| loc_establishment                          | Establishment        | Indicates the name of a place |
| loc_point_of_interest                      | Point of Interest    | Indicates a named point of interest. Typically, these "POI"s are prominent local entities that don't easily fit in another category such as "Empire State Building" or "Statue of Liberty." |
| loc_colloquial_area                        | Colloquial Area      | Indicates a commonly-used alternative name for the entity. |
| loc_natural_feature                        | Natural Feature      | Indicates a prominent natural feature. |
| loc_park                                   | Park                 | Indicates a named park. |
| loc_airport                                | Airport              | Indicates an airport. |
| loc_premise                                | Premise              | Indicates a named location, usually a building or collection of buildings with a common name like a plaza. |
| loc_street_number                          | Street Number        | Indicates a numerical street number |
| loc_route                                  | Route                | Indicates a named route; usually a street name |
| loc_intersection                           | Intersection         | Indicates a major intersection, usually of two major roads. |
| loc_locality                               | Locality             | Indicates an incorporated city or town political entity |
| loc_sublocality                            | Sublocality          | Indicates a first-order civil entity below a locality |
| loc_neighborhood_level_1                   | Neighborhood         | Indicates a neighborhood |
| loc_administrative_area_level_1            | Administrative Area Level 1 | Indicates a first-order civil entity below the country level. Within the United States, these administrative levels are states. Not all nations exhibit these administrative levels. |
| loc_administrative_area_level_2            | Administrative Area Level 2 | Indicates a second-order civil entity below the country level. Within the United States, these administrative levels are counties. Not all nations exhibit these administrative levels. |
| loc_administrative_area_level_3            | Administrative Area Level 3 | Indicates a third-order civil entity below the country level. This type indicates a minor civil division. Not all nations exhibit these administrative levels. |
| loc_postal_code                            | Postal Code         | Indicates a postal code as used to address postal mail within the country |
| loc_country                                | Country abbreviation| Indicates the national political entity. Two-letter abbreviation. |
| loc_country_long                           | Country name        | Long-form country name. |
| loc_latitude                               | Latitude            | Latitude coordinate of the geographic location.  Up to the fourth decimal place. |
| loc_longitude                              | Longitude           | Longitude coordinate of the geographic location.  Up to the fourth decimal place.|

<br><br>


---

<br><br>

## Examples

The following are examples that can be used as reference when integrating the API.

<br>


### Example: Get Posts

```
{
  "app_key": "###",
  "app_action": "post_get"
  "filter": {
    "location": {
      "latitude": 37.8033,
      "longitude": -122.2711,
      "radius": 2
      "secondary": true
    },
    "date": {
      "days": 7
    }
  }
}
```

<br>

### Example: Successful Method Response

```
{
  "code": 1,
  "message": "Post data retrieved",
  "data": {
    "posts": {
      {
        "post": {
          "po_title": "In tearful ceremony, Oakland street renamed for slain journalist Chauncey Bailey",
          "po_slug": "in-tearful-ceremony-oakland-street-renamed-for-slain-journalist-chauncey-bailey",
          "po_content": "He wanted us to carry a sense of pride, and valued the idea that every day, if we could just survive, live, grow and preserve our culture, that we are creating Black history," Bailey's son said at Saturday's ceremony.",
          "po_url_raw": "https://www.mercurynews.com/2022/03/05/in-tearful-ceremony-oakland-street-renamed-for-slain-journalist-chauncey-bailey/",
          "po_url_bloom": "https://www.bloom.li/map/article/krL2WNZ0d9PuK7M8lL18/in-tearful-ceremony-oakland-street-renamed-for-slain-journalist-chauncey-bailey",
          "po_image": "https://uploads.bloom.li/uploads/post/krL2WNZ0d9PuK7M8lL18/photo_default.jpeg",
          "po_publish_time": "2022-03-05 18:50:44",
          "po_created_time": "2022-03-05 18:50:46",
          "is_surrounding": false
        },
        "publisher": {
          "pub_name": "The Mercury News",
          "pub_username": "mercury-news",
          "pub_website_url": "www.mercurynews.com",
          "pub_website_url_raw": "https://www.mercurynews.com",
          "pub_color": "006DA3",
          "pub_image": "https://uploads.bloom.li/uploads/publisher/tlXL7KPVyXW4Kk49OE3B/photo_default.jpg",
          "pub_map_url": "https://www.mercurynews.com/news-near-me-map/"
        },
        "location": {
          "label": null,
          "distance_k": 0.76,
          "distance_m": 0.5,
          "loc_formatted_address": "14th Street & Lakeside Drive, Downtown Oakland, Oakland, CA",
          "loc_level": "intersection",
          "loc_intersection": "14th Street & Lakeside Drive",
          "loc_neighborhood_level_1": "Downtown Oakland",
          "loc_postal_code": "94612",
          "loc_locality": "Oakland",
          "loc_administrative_area_level_2": "Alameda County",
          "loc_administrative_area_level_1": "California",
          "loc_country": "US",
          "loc_country_long": "United States",
          "loc_latitude": 37.8010,
          "loc_longitude": -122.2629
        }
      }
    }
  }
}
```

<br>

### Example: Failed Method Response

```
{
  "code": 3,
  "message": "Invalid application key"
}
```
