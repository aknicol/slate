---
title: TripExpert API Reference

language_tabs:
  - shell

toc_footers:
  - <a href='https://www.tripexpert.com'>TripExpert</a>
  - <a href='https://www.tripexpert.com/api'>API Overview</a>
  - <a href='mailto:developer@tripexpert.com'>Email Support</a>

includes:

search: true
---

# Introduction

The TripExpert API provides access to expert review data for hotels, restaurants and tourist attractions from leading travel guides, magazines and newspapers.

**For more information about the API, including a summary of coverage and data available, and to request an API key, please visit our [API Overview](https://www.tripexpert.com/api).**


# Authentication

```shell
# Pass the API key as a URL parameter:
curl "https://api.tripexpert.com/v1/[endpoint]?api_key=xxx"
```

An API key is required to access to the API. Include your API key as the parameter `api_key` on all requests.


# Versioning

Minor revisions are made on an ongoing basis. Major backwards-incompatible changes are released as numbered versions. The current version of the API is accessible at the following endpoint:

`https://api.tripexpert.com/v1`

# Common Features

## Pagination

For the [Destination Index](#destination-index) and [Venue Index](#venue-index) endpoints, the following parameters can be used for pagination:

Parameter | Default | Description
--------- | ------- | -----------
limit | 100 | Number of records to return
offset | 0 | Record on which to start

The total number of available records for a query is indicated in the response as `total_records`.


## Geo Queries

For the [Destination Index](#destination-index) and [Venue Index](#venue-index) endpoints, results can be ordered by distance from a particular coordinate by passing `latitude` and `longitude` and setting `order_by` to `distance`.

The distance in miles to the relevant result is returned as `distance`.    



# Errors

HTTP error codes are used to indicate the status of a request. Codes in the 2xx range indicate success, codes in the 4xx range indicate an error resulting from the parameters provided, and codes in the 5xx range indicate an error with our service.

The request status is also returned in the `meta` node in the response.


# Venue Mapping

We use our own internal identifiers to reference venues. If you need to establish correspondence between our venue identifiers and those of a third party service (such as Booking.com, TripAdvisor, EAN, or Factual), we can send you a flat text file with this data. Please contact us for more information.


# Destinations

## Destination Index


```shell
curl "https://api.tripexpert.com/v1/destinations?order_by=distance&latitude=40.716236&longitude=-73.965237"

```

> Sample response snippet:

```json
{
	"id": "6",
	"name": "New York City",
	"country_name": "United States",
	"priority": 1,
	"permalink": "new-york-city",
	"index_photo": "https://static.tripexpert.com/images/destinations/index_photos/explore/6.jpg?1402263520",
	"distance": 3.4520683
}
```

This endpoint retrieves a list of destinations.

### HTTP Request

`GET https://api.tripexpert.com/v1/destinations`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
order_by | name | Sort order; options are `name`, `distance` and `priority` *
country_id | | [Country ID](#countries)
latitude |  | Required when `order_by` is `distance`
longitude |  | Required when `order_by` is `distance`

\* `priority` is an internal measure of the significance of a destination based on metrics such as demographics, number of venues and number of reviews.

# Venues

## Venue Index

```shell
curl "https://api.tripexpert.com/v1/venues?destination_id=6&amenity_ids[]=31&amenity_ids[]=32"

```

> Sample response snippet:

```json
{
	"id": "637972",
	"venue_type_id": 1,
	"name": "Greenwich Hotel",
	"tripexpert_score": 99,
	"rank_in_destintion": 1,
	"score": null,
	"index_photo": "https://static.tripexpert.com/images/venues/index_photos/index_retina/637972.jpg?1402340533"
}
```

This endpoint retrieves a list of venues in a specified destination.

### HTTP Request

`GET https://api.tripexpert.com/v1/venues`

### URL Parameters

Parameter | Default | Description
--------- | ------- | -----------
destination_id | | Destination ID
order_by | rank | Sort order. Options are `rank`, `tripexpert_score` and `distance`
latitude |  | Required when `order_by` is `distance`
longitude |  | Required when `order_by` is `distance`
venue_type_id |  | Options are `1` (hotels), `2` (restaurants), `3` (attractions)
amenity_ids |  | [Amenity IDs](#amenities) - supply as an array (see example at right)
category_ids |  | [Category IDs](#categories) - supply as an array
neighborhood_ids |  | [Neighborhood IDs](#neighborhoods) - supply as an array
price_category_ids |  | Options are `1` (budget hotels), `2` (midrange hotels), `3` (luxury hotels), `4` (budget restaurants), `5` (midrange restaurants), `6` (high-end restaurants) - supply as an array


## Venue Show

```shell
curl "https://api.tripexpert.com/v1/venues/636532?latitude=40.716236&longitude=-73.965237"

```

> Sample response snippet:

```json
{
"id": 21714,
"publication_id": 6,
"publication_name": "Fodors",
"extract": "Robert De Niro is an owner of the Greenwich Hotel in TriBeCa, De Niro's backyard and a neighborhood he's helped put on the map as a culinary and cultural benchmark.",
"source_url": "https://www.fodors.com/world/north-america/usa/new-york/new-york-city/hotels/reviews/the-greenwich-hotel-459127",
"publication_rating_name": "Choice"
}
```

This endpoint retrieves details for a specified venue. This includes all of the reviews available for the venue (see sample response at right). This is the primary API method for returning expert review data. 

<aside class="notice">
The number of reviews that are returned and the nature of information returned for each review depends on your level of API access.
</aside>

If `latitude` and `longitude` are supplied, the response will include the distance to the venue (in miles).

### HTTP Request

`GET https://api.tripexpert.com/v1/venues/<id>`

### URL Parameters

Parameter | Description
--------- | -----------
id | Venue ID  
latitude |  
longitude |  



# Hotel Pricing

```shell
curl "https://api.tripexpert.com/v1/venues?destination_id=6&limit=5&check_in=01/01/2016&check_out=01/02/2016"

```

> Sample response snippet:

```json
{
	"availability": "true",
	"rates": [
		{
			"provider": "Booking.com",
			"rate": "$4,500",
			"url": "https://www.booking.com/hotel/us/crosby-street.html?aid=381942&checkin_monthday=1&checkin_year_month=2016-01&checkout_monthday=2&checkout_year_month=2016-01"
		}
			]
}
```
The  [Venue Index](#venue-index) and [Venue Show](#venue-show) endpoints can also be used to return information about hotel room availability and pricing from TripExpert's OTA partners. This is done by passing (at a minimum) `check_in` and `check_out` dates.

<aside class="notice">
These methods are not available to all API users.
</aside>

### URL Parameters

In addition to the general parameters for the [Venue Index](#venue-index) or [Venue Show](#venue-show) calls:

Parameter | Default | Description
--------- | ------- | -----------
check_in | | Check-in date, mm/dd/yyyy
check_out | | Check-out date, mm/dd/yyyy
rooms | 1 | Number of rooms
guests | 2 | Number of guests
currency | USD | Currency code

<aside class="warning">
When using requesting pricing and availability data for multiple hotels, performance declines rapidly; avoid requesting more than 10 results at once.
</aside>





# Publications

```shell
curl "https://api.tripexpert.com/v1/publications"
```

> Sample response snippet:

```json
{
	"id": 2,
	"publication_name": "Lonely Planet",
	"icon": "https://static.tripexpert.com/publications/icons/retina/2.png?1400267529"
}
```
This endpoint returns a list of all of our source publications.

### HTTP Request

`GET https://api.tripexpert.com/v1/publications`






# Amenities

```shell
curl "https://api.tripexpert.com/v1/amenities"

```

> Sample response snippet:

```json
{
"id": 4,
"name": "Hot Tub"
}
```
This endpoint returns a list of hotel amenities. The amenity IDs can be used as filters for [Venue Index](#venue-index) queries.

### HTTP Request

`GET https://api.tripexpert.com/v1/amenities`





# Categories

```shell
curl "https://api.tripexpert.com/v1/categories?venue_type_id=2"
```

> Sample response snippet:

```json
{
"id": 8,
"name": "French"
}
```
This endpoint returns a list of categories. It takes a single parameter, `venue_type_id`.

Categories are cuisines for restaurants (`venue_type_id` 2) and classifications such as "museum", "outdoors", "historical" for amenities (`venue_type_id` 3). Hotels (`venue_type_id` 1) do not have categories.

The category IDs can be used as filters for [Venue Index](#venue-index) queries.

### HTTP Request

`GET https://api.tripexpert.com/v1/categories`





# Neighborhoods

```shell
curl "https://api.tripexpert.com/v1/neighborhoods?destination_id=6"
```

> Sample response snippet:

```json
{
"id": 24,
"name": "Lower East Side"
}
```
This endpoint returns a list of neighborhoods. It takes a single parameter, `destination_id`. Neighborhoods are not available for all destinations. The neighborhood IDs can be used as filters for [Venue Index](#venue-index) queries.

### HTTP Request

`GET https://api.tripexpert.com/v1/neighborhoods`






# Countries

```shell
curl "https://api.tripexpert.com/v1/countries"

```

> Sample response snippet:

```json
{
"id": 64,
"name": "Bhutan"
}
```
This endpoint returns a list of countries. The country IDs can be used as filters for [Destination Index](#destination-index) queries.

### HTTP Request

`GET https://api.tripexpert.com/v1/countries`
