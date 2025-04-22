Ref: https://www.gojek.io/blog/relooking-at-golangs-reflect-deepequal


Difference b/w comparable and deep equal

```go
func RemoveElementFromSlice[T comparable](slice []T, element T) []T {
	updatedSlice := make([]T, 0, len(slice))
	for _, item := range slice {
		if !reflect.DeepEqual(item, element) {
			updatedSlice = append(updatedSlice, item)
		}
	}
	return updatedSlice
}

func TestRemovePointerElementFromSlice(t *testing.T) {
	str1, str2, str3 := "a", "b", "c"
	slice := []*string{&str1, &str2, &str3}
	entry := &str2
	expected := []*string{&str1, &str3}

	result := RemoveElementFromSlice(slice, entry)
	assert.DeepEqual(t, expected, result)

	slice = []*string{&str1, &str2, &str3}
	anotherB := "b"
	anotherEntry := &anotherB
	result = RemoveElementFromSlice(slice, anotherEntry)
	assert.DeepEqual(t, expected, result)
}
```

if we use `item != element` instead of `!reflect.DeepEqual(item, element)`, the test would fail
