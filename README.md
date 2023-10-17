# go-querystring-wrike #

go-querystring-wrike is a Go library for encoding structs into URL query parameters. This Go library is a fork of the [go-querystring](https://github.com/google/go-querystring) Go library by Google. This library as been modified to encode structs to fit with the [Wrike API](https://developers.wrike.com/).

## Modifications ##
```go
// Example Wrike Struct
type CreateTasks struct {
	Title string `url:"title"`
	Dates struct {
		Type 			string  `url:"type"`
		Duration 		int  	`url:"duration,omitempty"`
		Start 			string  `url:"start,omitempty"`
		Due 			string  `url:"due,omitempty"`
		WorkOnWeekends 	bool  	`url:"workOnWeekends,omitempty"`
	} `url:"dates,omitempty,struct"`
	Followers []string  `url:"followers,omitempty,slice"`
	CustomFields []struct {
		Id 		string  `url:"id"`
		Value 	string  `url:"value"`
	} `url:"customFields,omitempty,slice+struct"`
}
```
Four new tags were added:

 - slice

```Go
// Original (go-querystring)
<url>?folowers=ABC123&followers=XYZ987

// New (go-querystring-wrike)
<url>?followers=["ABC123","XYZ987"]

```
- struct
```Go
// Original (go-querystring)
<url>?dates[type]=milestone&dates[start]=2023-09-18

// New (go-querystring-wrike)
<url>?dates={"type":"milestone","start":"2023-09-18"}
```
- slice+struct
```Go
// New (go-querystring-wrike)
<url>?dates=[{"id":"ABC123","value":"val1"},{"id":"XYZ987","value":"val2"}]
```
- bypass - This is a unique tag. The modified code will remove any structs within the parent struct that are empty and contain no values. There are some instances, however, when an empty struct is required. For example, when creating a new, blank project. This tag will allow that particular struct to be a valid URL parameter even though it is empty.
```Go
type  CreateFolders  struct {
	Title 			string  		`url:"title"`
	CustomFields 	[]CustomField 	`url:"customFields,omitempty,slice+struct"`
	CustomColumns 	[]string  		`url:"customColumns,omitempty,slice"`
	Project 		Project 		`url:"project,omitempty,struct,bypass"`
}
```

## Usage ##

```go
import "github.com/TGoers-FNSB/go-querystring-wrike/query"
```

go-querystring-wrike is designed to assist in scenarios where you want to construct a
URL using a struct that represents the URL query parameters.  You might do this
to enforce the type safety of your parameters.

The query package exports a single `Values()` function.  A simple example:

```go
type Options struct {
  Query   string `url:"q"`
  ShowAll bool   `url:"all"`
  Page    int    `url:"page"`
}

opt := Options{ "foo", true, 2 }
v, _ := query.Values(opt)
fmt.Print(v.Encode()) // will output: "q=foo&all=true&page=2"
```

## WrikeGo ##
This Go library is utilized in the WrikeGo Go library.