---
title: "CNBC Quote Server"
date: 2022-02-24
draft: false
description: "Quick toy server"
tags: [finance]
---

## Overview

I wanted a quick and dirty way to fetch price quotes from [CNBC's website](https://cnbc.com), so I threw together a quick Go server that does this for equities and futures.

This was purely for educational purposes only, so keep in mind that it may be against their [Terms of Service](https://www.cnbc.com/nbcuniversal-terms-of-service/) if you do end up actually using this for anything real.

## Implementation

```go
package main

import (
	"encoding/json"
	"fmt"
	"net/http"
	"strconv"

	"github.com/gocolly/colly/v2"
	"github.com/gorilla/mux"
	"strings"
)

type (
	Quote struct {
		Symbol string  `json:"symbol"`
		Price  float64 `json:"price"`
		Change float64 `json:"change"`
	}
)

func main() {
	r := mux.NewRouter()
	r.HandleFunc("/{symbol}", func(rw http.ResponseWriter, r *http.Request) {
		vars := mux.Vars(r)
		symbol := vars["symbol"]
		c := colly.NewCollector()

		c.OnHTML(".QuoteStrip-lastPriceStripContainer", func(e *colly.HTMLElement) {
			priceStr := e.ChildText(".QuoteStrip-lastPrice")
			priceStr = strings.Replace(priceStr, ",", "", -1)
			price, _ := strconv.ParseFloat(priceStr, 64)
			changeStr := e.ChildTexts(".QuoteStrip-changeDown span")[0]
			changeStr = strings.Replace(changeStr, ",", "", -1)
			change, _ := strconv.ParseFloat(changeStr, 64)

			quote := Quote{
				Symbol: symbol,
				Price:  price,
				Change: change,
			}

			response, _ := json.Marshal([]Quote{quote})
			rw.Write(response)
		})

		c.Visit(fmt.Sprintf("https://www.cnbc.com/quotes/%s", symbol))
	})
	http.Handle("/", r)
	http.ListenAndServe(":8080", r)
}
```

## Usage

```bash
❯ curl localhost:8080/@SP.1,@VX.1
[{"symbol":"@SP.1","price":4258.25,"change":-25.75},{"symbol":"@VX.1","price":29.15,"change":0.6013}]
```
