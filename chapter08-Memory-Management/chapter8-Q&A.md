# Chapter8️⃣. Memory Management

### Q. Segment의 길이는 length register와 segment table entry의 limit 중 어디에 위치해 있는가?

A. CPU가 전달하는 논리주소에는 segment number와 offset이 존재한다. 이 segment number를 통해서 length register에서 해당 segment table의 물리적 메모리 주소를 찾아서 접근, table에서 limit값을 확인할 수 있다.
