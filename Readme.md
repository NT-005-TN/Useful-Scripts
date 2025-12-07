# Git

## Скопировать все файлы\папки из одной ветки в другую через push

### 1. Сначала обновите информацию о удаленных ветках
git fetch origin

### 2. Переключитесь в вашу целевую ветку
git checkout feature/mobile-cards-qrcodes

### 3. Убедитесь, что ваша ветка актуальна
git pull origin feature/mobile-cards-qrcodes

### 4. Удалите ВСЕ тестовые файлы, которые есть сейчас (чтобы избежать конфликтов)
rmdir /S /Q "mobile\app\src\androidTest" 2>nul
rmdir /S /Q "mobile\app\src\test" 2>nul
rmdir /S /Q "tests" 2>nul

### 5. Скопируйте ВСЕ тестовые файлы из удаленной ветки feature/test-mobile-promotions
git checkout origin/feature/test-mobile-promotions -- mobile/app/src/androidTest
git checkout origin/feature/test-mobile-promotions -- mobile/app/src/test

### 6. Скопируйте ВСЕ другие тестовые файлы, которые могут быть в других местах
git checkout origin/feature/test-mobile-promotions -- tests 2>nul

### 7. Проверьте, какие еще тестовые файлы есть в ветке
git ls-tree -r origin/feature/test-mobile-promotions --name-only | findstr /i "test" > test_files.txt
type test_files.txt

### 8. Скопируйте все файлы, содержащие "test" в названии
for /f "tokens=*" %%i in (test_files.txt) do (
    echo Копирую: %%i
    git checkout origin/feature/test-mobile-promotions -- "%%i" 2>nul
)

### 9. Проверьте, что скопировалось
dir /s mobile/app/src/androidTest 2>nul
dir /s mobile/app/src/test 2>nul
dir /s tests 2>nul 2>nul

### 10. Добавьте все скопированные файлы в Git
git add mobile/app/src/androidTest
git add mobile/app/src/test
git add tests 2>nul

### 11. Проверьте статус
git status

### 12. Создайте коммит
git commit -m "feat: полностью импортированы все тестовые файлы из feature/test-mobile-promotions

- Android тесты из mobile/app/src/androidTest/
- Unit тесты из mobile/app/src/test/
- Все другие тестовые файлы из ветки"

### 13. Отправьте на сервер
git push origin feature/mobile-cards-qrcodes

### 14. Или более безопасный вариант - сначала проверьте, что будет изменено
git diff --name-only HEAD
