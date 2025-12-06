# Ревью исправлений https://github.com/MyNameIsN0body/hangman/tree/master/src/com/petproject/hangman
Для начала сделаю общее ревью в целом по коду. Затем пройдусь по твоим исправлениям предыдущего ревьювера.

## Структура файлов
Насколько я понял, ты писал в процедурном стиле, буду иметь ввиду.
Название класса `Pictures`. Множественные имена в большинстве случаев плохо. Лучше назвать `Picture`. Но заглянув в сам файл класса, можно понять, что и название `Picture` не подходит. Ниже объясню почему.
Ридми довольно понятное, лаконичное, даже не нужно картинок. Респект!

## Main
Насколько я понял, ты писал все в процедруном стиле, поэтому опущу моменты с разбиением на классы. Если вкратце, то с разбиением у тебя ушли бы все статичные методы, а переменные в методе `startGame` ушли бы в поля определенных классов.

Небольшое улучшение. У класса String есть метод `toCharArray()`, который переводит строку в массив символов.
```java
// До
private static char[] initHiddenWord(String secretWord) {
    char[] hiddenWord = new char[secretWord.length()];
    for (int i = 0; i < secretWord.length(); i++) {
        hiddenWord[i] = HIDDEN_LETTER_SYMBOL;
    }
    return hiddenWord;
}

// После
private static char[] initHiddenWord(String secretWord) {
    return secretWord.toCharArray();
}
```

Метод `userWantsToPlay` выбрасывает исключение, если ответ пользователя не считался. Не лучше ли будет вывести ошибку и вернуть false? Мой совет может оказаться спорным, поэтому лучше посоветоваться с другими ревьюверами.
```java
// Плохо
try {
    String input = reader.readLine();

    if (input.isEmpty()) {
        return true;
    } else {
        System.out.println("\uD83D\uDCAB Ждем вас снова! \uD83D\uDC4B До свидания!");
    }
} catch (IOException e) {
    throw new RuntimeException(e);
}

// Возможно хорошо
try {
    String input = reader.readLine();

    if (input.isEmpty()) {
        return true;
    } else {
        System.out.println("\uD83D\uDCAB Ждем вас снова! \uD83D\uDC4B До свидания!");
    }
} catch (IOException e) {
    e.printStackTrace(); // Можно ничего не писать. 
    return false;
}
```
В методе `readUserLetter` как будто то же самое. Но там ситуация немного иная. Ты при первой же ошибке считывания бросаешь исключение и завершаешь программу. Но не лучше ли будет при ошибке еще раз пробовать юзера ввести букву? То есть не весь цикл `do {} while() {}` обернуть в try-catch, а конкретно `input = reader.readLine();`:
```java
// До
try {
    do {
        System.out.println("✏️ Введите русскую букву:");
        input = reader.readLine();
    } while (!isValidInput(input));
} catch (IOException e) {
    throw new RuntimeException(e);
}

// После
// Upd: лучше посоветоваться с другими людьми, я тут могу очень сильно ошибаться.
do {
    System.out.println("✏️ Введите русскую букву:");
    try {
        input = reader.readLine();
    } catch(IOException e) {
        System.out.println("Ошибка ввода, попробуйте ввести еще"); // Можно еще добавить счетчик, если вдруг ошибка возникает несколько раз и все таки отпустить польщователя.
    }
} while (!isValidInput(input));
```
Метод `loadDictionary()` большой, дроби.
## Pictures
Название класса не соответствует его функциональности. Название Pictures навевает ощущение, что он тупо хранит картинки и свзяанные с ними операции, но на деле он занимается еще отрисовкой человечка в зависимости от его стейта(или `stages`). Лучше назвать его HangmanRenderer/HangmanDrawer/HangmanPrinter. 

## Исправления
Просмотрел твои исправления. Ты исправил почти все, кроме опечатки `playerLitter`, хах. В целом относишься ответственно к исправлениям, это большой плюс. У меня такое ощущение, что ты можешь сразу осилисть Симуляцию без написания Виселицы в ООП стиле.
