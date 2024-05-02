# Git Kullanımı

| GIT Commands                 | Description                                                                                                                                                     |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|` git checkout master          `| Localde master branche geri dönmenizi sağlar.                                                                                                                   |
|` git checkout -b _branchname_ `| _branchname_ adında yeni bir branch oluşturur, oluşturulan branch şuanki branch üzerine oluşturulur yani oluşturulan branch şuan bulunduğun branchi temel alır. |
|` git pull                     `| Localdeki kodu githubdaki repo ile senkorinize eder, github daki repoya eklenen yenilikler localinize yüklenir.                                                 |
|` git branch                   `| Local codebasenizdeki branchleri listeler.                                                                                                                      |
|` git status                   `| Localde hangi dosyalarda değişiklik yaptığınızı gösterir, commit attıktan sonra değişiklik listesi sıfırlanır.                                                  |
|` git add _filepath_           `| Belirttiğiniz dosyayı sonraki commite eklemini sağlar.                                                                                                          |
|` git add .                    `| Değişiklik yaptığınız tüm dosyaları sonraki commite eklemenizi sağlar.                                                                                          |
|` git commit -m "Commit Text"  `| Yaptığınız değişiklikleri commitlemeye yarar.                                                                                                                   |
|` git reset --hard HEAD^       `| Son yaptığınız commiti geri almanızı sağlar.                                                                                                                    |
|` git push origin _branch_     `| Commitleri github prnize göndermeyi sağlar, eğer daha pr açmadıysanız githubda reponun sayfasında pull request açabilirsiniz.                                   |

