# 扶輪網站開發

專案以 MVC 架構建立，前端開發者會用到的的資料夾為 :  
`view` 存放 html 檔案。  
`static` 存放 js, css, img 檔案。  
`pkg/xxx/controllers` route.go 裡為各種路徑名稱， handler.go 中為各個URL的驗證與回傳 models 的內容，以下用程式碼示意：  
```go
// route.go
func SetRoutes(e *echo.Echo, h *handler) {
	e.GET("/user", h.listUsersEndpoint)  // GET方法，撈出使用者列表
	e.POST("/user", h.postUserEndpoint) // POST方法，創建一個使用者
	e.GET("/user/:id", h.getUserEndpoint) // GET方法，撈出一個使用者
	e.PUT("/user/:id", h.updateUserEndpoint) // PUT方法，更新一個使用者的資料
	e.DELETE("/user/:id", h.deleteUserEndpoint) // DELETE方法，刪除一位使用者
}
```
`route.go` 中可以根據 `e.GET()` 確認該路徑方法為 GET，第一個參數`/user`為路徑名稱、第二參數`h.listUsersEndpoint`為處理的 function。  
接著是 `handler.go` 裡的其中一段程式：  
```go
// #########################################################
// GET  /user/:id  HTTP/1.1
// #########################################################

// UserResp 取得 User
type UserResp struct {
	Data user.User `json:"data"` // user.User 是指向 user.go 裡的 User struct
}

// getUserEndpoint get User
func (h *handler) getUserEndpoint(c echo.Context) error {
	var err error

	// param id
	id, err := strconv.Atoi(c.Param("id")) // 從 URL 上取出 id
	if err != nil {
		return errors.Wrap(errors.ErrInvalidInput, err.Error())
	}

	user, err := h.userSvc.GetUser(int64(id)) // models 中業務邏輯的處理
	if err != nil {
		return err
	}

	resp := &UserResp{ // 使用 struct UserResp 作為回傳的結構
		Data: user,
	}

	return c.JSON(http.StatusOK, resp) // 回傳內容為 JSON
}
```
這段程式碼中 `struct` 是 golang 的一種資料結構，與 C# 的 struct 是一樣的意思，在 `models/user.go`中就可以找到使用者的結構內容，而這個 getUserEndpoint 回傳的內容會根據 `struct` 裡定義的內容做回傳。
  
`pkg/xxx/models/xxx.go`  基本資料結構定義及業務邏輯方法在該檔案進行定義。  
`configs/app.yml` 存放 config 資料的地方，資料庫連線的帳密也在此。  

# How to install golang on windows 
https://oranwind.org/go-go-yu-yan-yu-windows-shang-zhi-an-zhuang-yu-huan-jing-she-ding/
  
# How to install golang on mac
https://ithelp.ithome.com.tw/articles/10200457

# 本地編譯指南
裝好 Golang 後請於小黑窗執行 `go env`，若能順利跑出東西則代表安裝 golang 成功，接著在專案資料夾運行 makefile 即可建立本地伺服器
```cmd
make build.run
```
