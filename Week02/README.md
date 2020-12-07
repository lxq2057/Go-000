## 作业
我们在数据库操作的时候，比如 `dao` 层中当遇到一个 `sql.ErrNoRows` 的时候，是否应该 `Wrap` 这个 `error`，抛给上层。为什么？应该怎么做请写出代码

## 思路
应该`Wrap`这个`error`，因为当前业务的后续查询和逻辑处理有可能受此结果的影响，所以应当交给`service`层消化此`error`

## 代码
```go
//dao
func (d *Dao) FindById(id int) (Sth, error) {
	sth := Sth{}
	DB := sql.DB{}
	err := DB.QueryRow("SELECT * FROM sth WHERE id=?", id).Scan(&sth.Id, &sth.Name)
	if err != nil {
		//....

		return sth, errors.Wrap(err, "....")
	}
	return sth, nil
}

//service
func (s *Service) GetById(id int) (string, error) {
	sth, err := s.dao.FindById(id)
	if err != nil {
		//....
		if errors.Is(err, sql.ErrNoRows) {
			//service层消化error或继续wrap
		}
		return "", err
	}
	return sth.Name, nil
}
```
