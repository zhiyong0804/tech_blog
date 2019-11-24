# Json

#### 解析
```go
type BasicInfo struct { Name string `json:"name"` Age int `json:"age"`
}
type JobInfo struct {
Skills []string `json:"skills"` }
type Employee struct {
BasicInfo BasicInfo `json:"basic_info"` JobInfo JobInfo `json:"job_info"`
}
```
* 反射机制性能比较低

#### easyjson使用
* easyjson -all structs.go
* 
