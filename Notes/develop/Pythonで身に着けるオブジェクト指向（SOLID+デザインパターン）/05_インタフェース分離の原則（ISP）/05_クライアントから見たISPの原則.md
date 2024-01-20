ISPに違反したインタフェースはインタフェースのクライアントにとって必要のないメソッドを公開することになる

```python
# 中略

instructor = CourseFromInstructor()
instructor.add_lecture("lecture")
instructor.review_cource() # review_coureseメソッドが呼び出せてしまう
```

ISPに違反しているクラスは
クライアントが使用しないメソッドを公開していしまう（公開は「潜在的な依存」）

