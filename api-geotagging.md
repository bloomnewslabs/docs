# Geotagging API documentation

Bloom's Geotagging API is available for third-party publishing applications to easily integrate geotagging capabilities for news content. Registered local news publishers on Bloom may then use the geotagging integration to save locations for stories and use those locations in hyperlocal experiences for readers and businesses.

The <a href="https://wordpress.org/plugins/bloom-for-publishers/" title="Bloom for Publishers WordPress plugin" target="_blank">"Bloom for Publishers" WordPress plugin</a> implements this API and can be used for websites using WordPress or as an additional reference for developers.

Use the following sections to learn how to use the API. If you have questions or run into an issue, please contact Stephen at steve@bloom.li.
<br><br>

### Table of Contents

1. [Configuration](#configuration)
2. [Authentication](#authentication)
3. [Methods](#methods)
    1. [Add Post](#add-post)
    2. [Draft Post](#draft-post)
    3. [Update Post](#update-post)
4. [Method Response](#method-response)
5. [Examples](#examples)
    1. [Add Post](#example-add-post)
    2. [Successful Method Response](#example-successful-method-response)
    3. [Failed Method Response](#example-failed-method-response)

<br><br>

---

<br><br>

## Configuration

The Geotagging API is organized with <a href="http://en.wikipedia.org/wiki/Representational_State_Transfer" title="REST" target="_blank">REST</a>.

All API methods use the URL Endpoint: [https://api.bloom.li/post/](https://api.bloom.li/post/) that must be accessed via HTTPS. Methods are only accessible using POST requests. The default quota is 1 call per 5 seconds, per publisher.

<br><br>

---

<br><br>

## Authentication

Each method call *must* include the following fields in order to authenticate the request. Additional requirements are listed within each method's definition below.

| Attribute       | Type        | Value Description                                    |
| --------------- | ----------- | ---------------------------------------------------- |
| app_key         | String      | Application key                                      |
| app_publisher   | String      | Publisher key                                        |
| app_action      | String      | Method slug. Provided in each method definition below|
| google_key      | String      | Google API Key                                       |
| plugin_system   | String      | Name of CMS or publishing tool. Ex. "WordPress"      |
| plugin_version  | Float       | Version of the *plugin_system*                       |

<br>

### Additional Authentication Instructions


#### Application and Publisher Keys
Follow these instructions to obtain these keys:
1. Login to your Bloom account at <a href="https://www.bloom.li" title="Bloom Labs" target="_blank">bloom.li</a>
2. Select a Publisher in the <a href="https://www.bloom.li/account/publishers" title="Publisher Page on Bloom" target="_blank">Publisher's Page</a>
3. Go to the "Service Settings" tab
4. Find the Application Key and Publisher Key that are in the Account Keys section


#### Google API Key
Follow these instructions to obtain this key:
1. Visit the <a href="https://console.developers.google.com/apis" title="Google API page" target="_blank">Google API page</a>
2. Select or Create a Project
3. In the <a href="https://console.developers.google.com/apis/library" title="Google API Library" target="_blank">Google API Library</a>, search for and enable the "Google Maps JavaScript API" and "Google Maps Geocoding API"
4. In <a href="https://console.developers.google.com/billing/" title="Google API Billing" target="_blank">Google API Billing</a>, add a billing account. It is required to add this before the APIs can be used (<a href="https://support.google.com/googleapi/answer/6158867?hl=en" title="More information about Google API Billing" target="_blank">More information</a>)
5. Go to the <a href="https://console.developers.google.com/apis/credentials" title="Google API Credentials" target="_blank">Google API Credentials</a> page and follow the steps to create your API key

<br><br>

---

<br><br>

## Methods

The following methods can be used to perform [Add](#add-post), [Draft](#draft-post), and [Update](#update-post) actions for geotagging a news story or event ("Post"). Each method is explained below with the input that's required or optional for a single call.

After the call, a [Response](#method-response) will be returned with information about the request's result.

<br>

### Add Post
This method will geotag a Post. [View example](#example-add-post).

*Requires attributes from [Authentication](#authentication).*


| Attribute       | Type        | Value Description                                |
| --------------- | ----------- | ------------------------------------------------ |
| app_action      | String      | "post_add"                                       |
| draft_key       | String      | *Optional.* The *key* of an existing Post draft, if publishing.        |
| type            | String      | *Optional.* Type of Post. Options: "news" (default), "event", "emergency"  |
| url             | String      | *Optional.* URL of the Post. Domain must be the same as the website registered on Bloom for the publisher. |
| title           | String      | Title of the Post. Must be between 10 and 150 characters.   |
| content         | String      | Excerpt or Description of Post. Must be between 10 and 1,000 characters.  |
| content_full    | String      | Full text of Post. Must be no more than 500 words. This is not made public, it is only used to automatically detect categories for filtering or personalization.    |
| callout         | String      | *Optional.* Text for call-to-action (CTA) button on Bloom services, such as plugins and newsletters.        |
| image_url       | String      | *Optional.* Image URL of Post. Accepted extensions: jpg, jpeg, png, gif. Default: Image of map screenshot of the Post's location.   |
| date            | Timestamp    | *Optional.* Publish date of Post. Default: Date and time of method call    |
| expiration_days | Integer     | *Optional.* Number of days after the Post's *date* until Bloom should archive. Archived post's are automatically excluded from public-facing tools on Bloom, such as plugins and newsletters, unless the owner of the tool has specified to show archived post's. Default: 10 days for "news" and "emergency" post *type*, 4 days for "event" post *type*. Maximum of 30 days allowed.   |
| locations       | Array of [Location Input Objects](#location-input-object)       | One or more locations for the Post. Up to 30 total locations can be accepted.    |
| custom_fields   | Array of [Custom Fields](#custom-field-options)       | Custom fields for post *type*.   |


<br><br>


### Draft Post
This method will save a draft of the geotagged Post to be published at a later time.

*Requires attributes from [Authentication](#authentication).*

| Attribute       | Type        | Value Description                                |
| --------------- | ----------- | ------------------------------------------------ |
| app_action      | String      | "post_draft"                                       |
| key             | String      | *Optional.* The *key* of an existing Post draft, if updating.         |
| type            | String      | *Optional.* Type of Post. Options: "news" (default), "event", "emergency"  |
| url             | String      | *Optional.* URL of the Post. Domain must be the same as the website registered on Bloom for the publisher. |
| title           | String      | Title of the Post. Must be between 10 and 150 characters.   |
| content         | String      | Excerpt or Description of Post. Must be between 10 and 1,000 characters.  |
| callout         | String      | *Optional.* Text for call-to-action (CTA) button on Bloom services, such as plugins and newsletters.        |
| image_url       | String      | *Optional.* Image URL of Post. Accepted extensions: jpg, jpeg, png, gif. Default: Image of map screenshot of the Post's location.   |
| date            | Timestamp    | *Optional.* Publish date of Post. Default: Date and time of method call    |
| locations       | Array of [Location Input Objects](#location-input-object)       | One or more locations for the Post. Up to 30 total locations can be accepted.    |
| custom_fields   | Array of [Custom Fields](#custom-field-options)       | Custom fields for post *type*.   |


<br><br>


### Update Post
This method will update an existing published (not drafted) Post.

*Requires attributes from [Authentication](#authentication).*

| Attribute       | Type        | Value Description                                |
| --------------- | ----------- | ------------------------------------------------ |
| app_action      | String      | "post_update"       |
| key             | String      | The *key* of the existing Post.     |
| status          | String      | *Optional.* Options: "active" or "inactive" |
| type            | String      | *Optional.* Type of Post. Options: "news" (default), "event", "emergency"  |
| url             | String      | *Optional.* URL of the Post. Domain must be the same as the website registered on Bloom for the publisher. |
| title           | String      | Title of the Post. Must be between 10 and 150 characters.   |
| content         | String      | Excerpt or Description of Post. Must be between 10 and 1,000 characters.  |
| callout         | String      | *Optional.* Text for call-to-action (CTA) button on Bloom services, such as plugins and newsletters.        |
| image_url       | String      | *Optional.* Image URL of Post. Accepted extensions: jpg, jpeg, png, gif. Default: Image of map screenshot of the Post's location.   |
| has_image       | Boolean     | *Optional.* Removes Post's image if this is set to "0" and *image_url* is not provided.    |
| date            | Timestamp    | *Optional.* Publish date of Post. Default: Date and time of method call    |
| locations       | Array of [Location Input Objects](#location-input-object)       | One or more locations for the Post. Up to 30 total locations can be accepted.    |
| custom_fields   | Array of [Custom Fields](#custom-field-options)       | Custom fields for post *type*.   |

<br><br>


### Method Objects

#### Location Input Object

| Attribute       | Type        | Value Description                                |
| --------------- | ----------- | ------------------------------------------------ |
| rank            | Integer     | Numerical rank of location. Options: "1" for primary location or "2" for secondary location.    |
| address         | String      | Full address of location   |
| label           | String      | *Optional.* Label or short description about the location. This should be an alternative name for the location address, such as "The Coffee Shop" or "Office of the Company's President". Limited to 130 characters.    |
| text            | String      | *Optional.* Long description about how the location is related to the story.  Limited to 500 characters.    |
| latitude        | Float       | *Optional.* Latitude coordinate    |
| longitude       | Float       | *Optional.* Longitude coordinate    |

<br>

Notes about Location Input Object:
1. Latitude and Longitude: These coordinates are optional but recommended. Both should be provided together or excluded altogether. They are used as an alternative for recognizing the location if the *address* is cannot be located.
2. Address: The location's address is recommended to be as geographically specific as possible in order to ensure geotagging of requested location. Bloom does have specificity requirements that will accept locations at the level of "administrative area level 3" or smaller, as defined by Google's <a href="http://googlemaps.github.io/google-maps-services-java/v0.1.3/javadoc/com/google/maps/model/AddressComponentType.html" title="Google's Address Components" target="_blank">Address Components</a>.


<br>

#### Custom Field Options

| Attribute       | Type        | Value                                            |
| --------------- | ----------- | ------------------------------------------------ |
| end_date        | Timestamp   | End date for "event" post *type* only            |


<br><br>

---

<br><br>

## Method Response

Upon a method call, the following data will be returned as a JSON object. Responses should first be checked by their *success* and *code* attributes. If either of those show the method call failed, the *code* or *message* should be used to identify the issue. The *data* attribute/object may not be available for unsuccessful method calls.

 View examples of [Successful Call](#example-successful-method-call) and [Failed Call](#example-failed-method-call).

| Attribute                              | Type       | Value Description            |
| -------------------------------------- | ------------------------------------------------ | --------------- |
| success                                | Boolean    | Whether or not a connection was made. If false, it is likely due to an error with the integration or internet connection. |
| code                                   | Number     | The type of response. See [Response Codes](#response-codes) section. If is unsuccessful code (anything other than "1"), it is likely due to an input error. |
| message                                | String     | A message describing the method's response code. |
| data                                   | Array      | Data about the Post that was geotagged or updated. |
| data.key                               | String     | Key of geotagged Post on Bloom |
| data.url                               | String     | URL of geotagged Post on <a href="https://www.bloom.li/map" title="Bloom's map" target="_blank">Bloom's map</a> |
| data.date                              | Timestamp  | Date and time the method call was completed. Only returned with successful *post_update* method calls. |
| data.location                          | [Location Response Object](#location-response-object)     | Location Object of Post's primary location (rank 1) |
| data.location_errors                   | Array of [Location Error Objects](#location-error-response-object)      | List of errors with any of the requested locations |
| data.categories                        | Array      | *Only returned with "post_add" requests.* Array of category names identified from Post's *content_full* field using automated categorization. |


<br><br>

### Response Codes

Each method call returns a *code* that represents a specific type of successful or failed response.

- **1:** Method call completed without any error.
- **2:** Method call failed because the following required field(s) were not provided: *(List of fields)*
- **3:** Invalid Application Key provided in *app_key* field
- **4:** Inactive Application Key provided. Geotagging access is currently disabled for this account.
- **5:** Invalid Publisher Key provided in *app_publisher* field or the associated Publisher does not have geotagging access.
- **6:** Rate limit exceeded. Limited to 1 method call per 5 seconds.
- **7:** Invalid data given in fields. Error message will have details on the particular fields that need to be resolved.
- **8:** Invalid location given.
- **9:** Location given is not specific enough.
- **10:** Invalid Post URL provided. URL format error
- **11:** Invalid Post URL provided. Publisher does not have access to publish on behalf of the URL's domain.
- **12:** Invalid Post URL provided. URL has already been submitted. Returns *key* of the Post with the URL (i.e., *data.key*).
- **13:** Invalid image provided. Error message will have details.
- **14:** Invalid post date or expiration days provided.
- **15:** Location is out of allowed geotagging range for given publisher. Returns the *distance* (in kilometers) that the location was out of range from the Publisher's geotagging limit. (i.e., *data.distance*)
- **16:** Invalid option provided in *status* field during a Post Update
- **17:** Geotagging access is disabled for source/version
- **18:** Invalid option provided in *type* field

<br><br>

### Location Response Object

The Location Response Object is included in the response from a [Method](#method) call. Depending on the location geotagged, not all attributes below will be included.

| Attribute                              | Name                 | Value Description            |
| -------------------------------------- | ------------------------------------------------ | --------------- |
| formatted_address                      | Formatted Address    | Always included in the Location Object. A human-readable address of this location. It is generally composed of one or more the components below. |
| level                                  | Level                | The geographic depth of the location. Value is equal to one of the attributes below that best describe the depth.  |
| establishment                          | Establishment        | Indicates the name of a place |
| point_of_interest                      | Point of Interest    | Indicates a named point of interest. Typically, these "POI"s are prominent local entities that don't easily fit in another category such as "Empire State Building" or "Statue of Liberty." |
| colloquial_area                        | Colloquial Area      | Indicates a commonly-used alternative name for the entity. |
| natural_feature                        | Natural Feature      | Indicates a prominent natural feature. |
| park                                   | Park                 | Indicates a named park. |
| airport                                | Airport              | Indicates an airport. |
| premise                                | Premise              | Indicates a named location, usually a building or collection of buildings with a common name like a plaza. |
| street_number                          | Street Number        | Indicates a numerical street number |
| route                                  | Route                | Indicates a named route; usually a street name |
| intersection                           | Intersection         | Indicates a major intersection, usually of two major roads. |
| locality                               | Locality             | Indicates an incorporated city or town political entity |
| sublocality                            | Sublocality          | Indicates a first-order civil entity below a locality |
| neighborhood_level_1                   | Neighborhood         | Indicates a neighborhood |
| administrative_area_level_1            | Administrative Area Level 1 | Indicates a first-order civil entity below the country level. Within the United States, these administrative levels are states. Not all nations exhibit these administrative levels. |
| administrative_area_level_2            | Administrative Area Level 2 | Indicates a second-order civil entity below the country level. Within the United States, these administrative levels are counties. Not all nations exhibit these administrative levels. |
| administrative_area_level_3            | Administrative Area Level 3 | Indicates a third-order civil entity below the country level. This type indicates a minor civil division. Not all nations exhibit these administrative levels. |
| postal_code                            | Postal Code         | Indicates a postal code as used to address postal mail within the country |
| country                                | Country abbreviation| Indicates the national political entity. Two-letter abbreviation. |
| country_long                           | Country name        | Long-form country name. |
| latitude                               | Latitude            | Latitude coordinate of the geographic location.  Up to the fourth decimal place. |
| longitude                              | Longitude           | Longitude coordinate of the geographic location.  Up to the fourth decimal place.|

<br><br>

### Location Error Response Object

The Location Error Response Object is included in the response from a [Method](#method) call.

| Attribute       | Type       | Value Description            |
| --------------- | ---------- | ---------------------------- |
| code            | Number     | Response code of the type of error; Options: "0" = Invalid location address; "8" = Location could not be geocoded correctly; "9" = Location does not meet Bloom's specificity requirements |
| message         | String     | Friendly message about the error  |
| input           | Array      | Input provided for the location  |
| input.rank      | Integer    | Rank: "1" = Primary; "2" = Secondary  |
| input.label     | String     | Short label  |
| input.text      | String     | Text description  |
| input.address   | String     | Full address  |
| input.latitude  | Float      | Latitude coordinate  |
| input.longitude | Float      | Longitude coordinate  |

<br><br>

---

<br><br>

## Examples

The following are examples that can be used as reference when integrating the API.

<br>

### Example: Add Post

```
{
  "plugin_system": "wordpress",
  "plugin_version": "1.6.17",
  "google_key": "###",
  "app_key": "###",
  "app_publisher": "###",
  "app_action": "post_add"
  "date": "2021-09-29 17:40:30-07:00",
  "expiration_days": 20,
  "type": "news",
  "title": "San Jose plans dynamic initiative to house and employ homeless",
  "content": "Responding to the growing homeless crisis, San Jose has ambitious plans to shelter and employ thousands of unhoused residents within the next five years.",
  "content_full": "Responding to the growing homeless crisis, San Jose has ambitious plans to shelter and employ thousands of unhoused residents within the next five years. The Rules and Open Government Committee voted unanimously Wednesday to recommend the City Council support a housing blueprint and efforts to employ homeless residents to clean up the city. The council will consider the plan at a later date.",
  "url": "https://sanjosespotlight.com/san-jose-plans-dynamic-initiative-to-house-and-employ-homeless/",
  "image_url": "https://sanjosespotlight.s3.us-east-2.amazonaws.com/wp-content/uploads/2021/06/02140350/Sanctioned-Encampment-Pic-scaled.jpg",
  "locations": {
    {
      "rank": 1,
      "label": "Interim housing site",
      "address": "5862 Rue Ferrari, Edenvale, San Jose, CA",
      "latitude": 37.2473104,
      "longitude": -121.7760768
    }
  }
}
```

<br>

### Example: Successful Method Response

```
{
  "code": 1,
  "message": "Post has been geotagged",
  "data": {
    "key": "EFRv2fA7Z6EVHW4VS8UH",
    "url": "https://www.bloom.li/map/article/EFRv2fA7Z6EVHW4VS8UH/san-jose-plans-dynamic-initiative-to-house-and-employ-homeless",
    "location": {
      "formatted_address": "5862 Rue Ferrari, Edenvale, San Jose, CA",
      "street_number": 5862,
      "route": "Rue Ferrari",
      "locality": "San Jose",
      "neighborhood_level_1": "Edenvale
      "administrative_area_level_1": "California
      "administrative_area_level_2": "Santa Clara County
      "postal_code": 95138,
      "country": "US",
      "country_long": "United States",
      "latitude": 37.2473,
      "longitude": -121.7761,
      "level": "street_number",
    }
  }
}
```

<br>

### Example: Failed Method Response

```
{
  "code": 8,
  "message": "No valid location provided"
}
```
