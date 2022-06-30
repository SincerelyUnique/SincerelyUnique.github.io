---
title: 简单使用Gin框架
date: 2022-03-06 09:28:47
tags:
- go
- gin
categories:
---

> OS： windows
> go： 1.17.2

参考： 
1. https://go.dev/doc/tutorial/web-service-gin
2. https://github.com/gin-gonic/gin#installation

# 创建文件夹并初始化项目骨架

## 创建web-service-gin文件夹
> $ mkdir web-service-gin

## 进入web-service-gin文件夹
> $ cd web-service-gin

## Go mod初始化
> $ go mod init example/web-service-gin

## 获取gin依赖
> $ go get -u github.com/gin-gonic/gin

# 编码
1. 在web-service-gin文件夹下创建main.go文件
2. 将main.go文件首行改为： `package main`
3. 编码完成后在web-service-gin文件夹下执行： `go run .`

```go
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

// album represents data about a record album.
type album struct {
	ID     string  `json:"id"`
	Title  string  `json:"title"`
	Artist string  `json:"artist"`
	Price  float64 `json:"price"`
}

// albums slice to seed record album data.
var albums = []album{
	{ID: "1", Title: "Blue Train", Artist: "John Coltrane", Price: 56.99},
	{ID: "2", Title: "Jeru", Artist: "Gerry Mulligan", Price: 17.99},
	{ID: "3", Title: "Sarah Vaughan and Clifford Brown", Artist: "Sarah Vaughan", Price: 39.99},
}

// getAlbums responds with the list of all albums as JSON.
func getAlbums(c *gin.Context) {
	c.IndentedJSON(http.StatusOK, albums)
}

// postAlbums adds an album from JSON received in the request body.
func postAlbums(c *gin.Context) {
	var newAlbum album

	// Call BindJSON to bind the received JSON to
	// newAlbum.
	if err := c.BindJSON(&newAlbum); err != nil {
		return
	}

	// Add the new album to the slice.
	albums = append(albums, newAlbum)
	c.IndentedJSON(http.StatusCreated, newAlbum)
}

// getAlbumByID locates the album whose ID value matches the id
// parameter sent by the client, then returns that album as a response.
func getAlbumByID(c *gin.Context) {
	id := c.Param("id")

	// Loop over the list of albums, looking for
	// an album whose ID value matches the parameter.
	for _, a := range albums {
		if a.ID == id {
			c.IndentedJSON(http.StatusOK, a)
			return
		}
	}
	c.IndentedJSON(http.StatusNotFound, gin.H{"message": "album not found"})
}

func main() {
	router := gin.Default()
	router.GET("/albums", getAlbums)
	router.POST("/albums", postAlbums)
	router.GET("/albums/:id", getAlbumByID)

	router.Run("localhost:8080")
}
```

# 测试

## 测试Get请求，获取所有数据

```
D:\goland\goproject\web-service-gin>curl http://localhost:8080/albums
[
    {
        "id": "1",
        "title": "Blue Train",
        "artist": "John Coltrane",
        "price": 56.99
    },
    {
        "id": "2",
        "title": "Jeru",
        "artist": "Gerry Mulligan",
        "price": 17.99
    },
    {
        "id": "3",
        "title": "Sarah Vaughan and Clifford Brown",
        "artist": "Sarah Vaughan",
        "price": 39.99
    }
]
```

## 测试post请求，添加1条数据
```
D:\goland\goproject\web-service-gin>curl http://localhost:8080/albums --include --header "Content-Type: application/json" --request "POST" --data "{\"id\": \"4\",\"title\": \"The Modern Sound of Betty Carter\",\"artist\": \"Betty Carter\",\"price\": 49.99}"
HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Thu, 30 Jun 2022 04:16:00 GMT
Content-Length: 116

{
    "id": "4",
    "title": "The Modern Sound of Betty Carter",
    "artist": "Betty Carter",
    "price": 49.99
}
```
添加后获取一下
```
D:\goland\goproject\web-service-gin>curl http://localhost:8080/albums
[
    {
        "id": "1",
        "title": "Blue Train",
        "artist": "John Coltrane",
        "price": 56.99
    },
    {
        "id": "2",
        "title": "Jeru",
        "artist": "Gerry Mulligan",
        "price": 17.99
    },
    {
        "id": "3",
        "title": "Sarah Vaughan and Clifford Brown",
        "artist": "Sarah Vaughan",
        "price": 39.99
    },
    {
        "id": "4",
        "title": "The Modern Sound of Betty Carter",
        "artist": "Betty Carter",
        "price": 49.99
    }
]
```

## 测试通过id查询数据
```
D:\goland\goproject\web-service-gin>curl http://localhost:8080/albums/2
{
    "id": "2",
    "title": "Jeru",
    "artist": "Gerry Mulligan",
    "price": 17.99
}
```
