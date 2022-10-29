---
layout: post
title: "· 【IntelliJ】 Execution failed for task :test. 오류"
nav_order: 3
parent : Error
grand_parent: 📚Learned
permalink: docs/Learned/Error/TestError
---

# "Execution failed for task ':test'." 오류

<br>

<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019111903122.png" alt="image-20221019111903122" style="zoom:80%;" />
</p>

테스트 코드를 작성하고 테스트를 했는데



`Execution failed for task ':test'.` 라고 뜨면서 리포트 파일이 생성되었다고 한다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019111915148.png" alt="image-20221019111915148" style="zoom:80%;" />
</p>

알려준 위치로 가서 `html` 파일을 열어봤더니, 테스트 결과를 알려주긴 했다.



근데 나는 `intellij` 로 바로 결과를 확인하고 싶어서 방법을 찾아보았다.


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019111928762.png" alt="image-20221019111928762" style="zoom:80%;" />
</p>


`file → Settings → Build, Execution, Deployment → Gradle → Run tests using` 을 `itellij` 로 변경


<p align="center">
<img src="https://raw.githubusercontent.com/buinq/imageServer/main/img/image-20221019111945139.png" alt="image-20221019111945139" style="zoom:80%;" />
</p>


변경 후, 잘 동작한다. ㅎㅎ