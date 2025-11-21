# Ревью https://github.com/dab1231/task
## Баги
Нажимаю 1 пункт меню и загаданное слово сразу показывается:

<img width="481" height="486" alt="image" src="https://github.com/user-attachments/assets/a15c2da1-1075-4913-bf8a-78fa8cb7600e" />

Также ошибка вывелась, если в меню написать букву, а не число(Более подробно распишу нижу в классе `RunGame`):
<img width="890" height="424" alt="image" src="https://github.com/user-attachments/assets/29c1e979-4484-4eb9-9e05-77fa539897c9" />

## Readme
Некритично, но информация про то, что искходный код лежит в src, а в libs лежат библиотеки - бесполезна. Куда лучше будет, если ты сделаешь описание игры, скриншоты. Чем красивше, тем больше вероятность, что твой репозиторий заметят. 

## gitignore
Почитай про гит и гитигнор(что можно добавлять туда). У гитхаба есть шаблон на разные языки. Допустим у тебя лежит .vscode. Этот файл только для твоей ide, поэтому для других людей он будет бесполезным. Стоит его добавить в .gitignore. А вообще лучше использовать Intellij Idea, красиво модно молодежно.

## Class файлы
Их стоит убрать или в конкретную папку положить. Если билдишь через консоль, то ты можешь указать папку, куда эти .class файлы кидать. Обычно это папка bin.

## RunGame
Советую почитать новенции по неймингу классов, методов, пакетов и вообще всего. Классы - существительные, методы в большинстве случаев - глаголы.
Именование flag непонятное. Давай именам более содержательные имена. Пишем код один раз, читаем - много.
```java
// Неправильно
boolean flag = true;
 while (flag) {
  //...
 }

// Правильно
boolean isGameRunning = true;
 while (isGameRunning) {
  //...
 }
```
А еще лучше назвать `isRunning` и положить в класс Game(применение `game.isRunnung()` звучит неплохо, не так ли?). Также `game.steps` как-будто хочется скрыть от других классов, ибо зачем им знать внутрянку игры? Цикл игры лучше реализовывать в классе Game, потому что это его зона ответственности. Итого:
```java
// После всех улучшений
switch (point) {
 case 1:
  Game game = new Game();
  game.start();
 case 2:
  System.exit(0);
}
```
Не пробрасывай исключения в никуда и по возможности обрабатывай их сразу. В данном случае ты пробрасываешь вверх из `main`, а выше уже не обработаешь по понятной причине. В коде ниже в catch я обработал ошибку, если вдруг введут не Integer или ничего не введут. В некоторых случаях можно оставить catch пустым, но только тогда, когда это уместно(в данном случае нет).
```java
// Плохо
public static void main(String[] args) throws IOException {

// Хорошо
try {
 int point = scanner.nextInt();
} catch(IOException e) {
 System.out.println("Такого варианта ответа не существует! Введите 1 или 2.");
}
```
Имена методов начинаются с маленькой буквы:
```java
// Неправильно
win = game.Step();

// Правильно
win = game.step();
```

## Game
Поля, которые в зоне ответственности определенного экземпляра класса должны быть приватными
```java
// Очень плохо
public char[] word;
public char[] guess;
public int steps = 6;
public List<Character> validLetters = Arrays.asList('a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z');
public List<Character> inputedLetters = new ArrayList<>();

// Хорошо
private char[] word;
private char[] guess;
private int steps = 6;
private List<Character> validLetters = Arrays.asList('a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z');
private List<Character> inputedLetters = new ArrayList<>();
```
Также проброс исключения вверх без его обработки. Также очень плохо жестко инициализировать поля конкретными значениями, т.к. класс становится не переиспользуемым. То есть если захочешь экземпляр класса с словами и ответами, то увы и ах - сделать не сможешь. Если нужны еще пояснения с примерами, u are welcome [IT Mentor tg](https://web.telegram.org/k/#@zhukovsd_it_chat).
```java
// Плохо
public Game() throws IOException {
 word = getChars(Generate.GenerateWord());
 guess = makeGuess();
}
// Хорошо
public Game(char[] word, char[] guess) {
 this.word = word;
 this.guess = guess;
}
```

НЕЛЬЗЯ возвращать поле, если ты его меняешь в этом же методе. Этим страдают методы getChars и makeGuess. Также названия этих методов непонятны.
```java
// Плохо
public char[] getChars(String word) {
    this.word = new char[word.length()];
    for (int i = 0; i < word.length(); i++) {
        this.word[i] = word.charAt(i);
    }
    return this.word;
}

// Хорошо
public void extractChars(String word) {
    this.word = new char[word.length()];
    for (int i = 0; i < word.length(); i++) {
        this.word[i] = word.charAt(i);
    }
}

// Либо так, что более предпочтительно, ниже объясню почему
public char[] extractChars(String word) {
    char[] extracted = new char[word.length()];
    for (int i = 0; i < word.length(); i++) {
        extracted[i] = word.charAt(i);
    }

    return extracted;
}
```
А вообще метод просто вычленяет символы из строки и сохраняет в массив. Game не должен заниматься этим, это задача для какого-нибудь утилитного класса либо же такой метод уже есть или же уже есть такой метод в Java или вообще стоит подумать, а стоит ли вообще строку переводить в символы? А может сразу передавать массив символов в класс? 
```java
// Хорошо, где-то там в RunGame.java
char[] extractedCharsFromString = StringUtil.extractChars(Generate.GenerateWord());
Game game = new Game(extractedCharsFromString, guessWord);
```
Есть подозрения, что метод makeGuess содержит странную логику, что багует игру. Но это оставлю тебе будущему поискать отлавливать баг. Также не игнорируй подсказки IDE, но и не слепо верь. Насколько я понял, у тебя не Intellij IDEA, поэтому скорее всего и не увидел данную подсказку.
<img width="273" height="193" alt="image" src="https://github.com/user-attachments/assets/6209fdc0-63ff-4f0c-86c3-4840cedce477" />
```java
// Плохо
public char[] makeGuess() {
    this.guess = new char[word.length];
    Random random = new Random();
    for(int i = 0; i < guess.length; i++){
        guess[i] = '_';
    }
}
// Хорошо
public char[] makeGuess() {
    this.guess = new char[word.length];
    Random random = new Random();
    Arrays.fill(guess, '_'); // IDE подсказала, что есть такой метод в Arrays
}
```
Боль и страдания! Метод Step слишком большой. Если метод не помещается в экран монитора - дроби. Примера хорошего дробления не будет, тоже оставлю на тебя. Лишь скажу, что у тебя дубляж кода есть в этом же методе. Причем 3 раза так точно. Если 2 раза, я бы забил на это, но 3 раза уже многовато.
```java
// Плохо
clearConsole();
inputedLetters.add(letter);
return IsWon(guess);
//...
clearConsole();
inputedLetters.add(letter);
return IsWon(guess);
//...
clearConsole();
inputedLetters.add(letter);
return IsWon(guess);

// Хорошо
public boolean step() {// Название тож не очень и зачем boolean возвращать? Это же метод сделать шаг, а не что-то проверять.
 return someStepCheck();
}
//...
private boolean someStepCheck() { // Подбери грамотное название, т.к. это для примера написал
 clearConsole();
 inputedLetters.add(letter);
 return IsWon(guess);
}
```
Методы `clearConsole()`, `DrawGallows()` не относятся к классу Game. Игра не должна заниматься отрисовкой. Также у тебя дублирование кода в `DrawGallows()`. Если внимательно посмотреть, то у тебя алгоритм конкретного кейса идет по одному и тому же алгоритму:
1. Отрисовать человечка
2. Отрисовать количество попыток
3. Вывести слово
4. Вывести guess
```java
// Плохо
System.out.println("""
    +---+
    |   |
    O   |
   /|\\  |
   / \\  |
        |
________|
0 попыток(
""");
System.out.println(word);
System.out.println(guess);
// Хорошо
switch(steps) {
    case 0:
        draw(hangmanPicture0, steps);
        break;
    case 1:
        draw(hangmanPicture1, steps);
        break;
    case 2:
        draw(hangmanPicture2, steps);
        break;
    case 3:
        draw(hangmanPicture3, steps);
        break;
    case 4:
        draw(hangmanPicture4, steps);
        break;
//...
}
private void draw(String hangmanPicture, int attempts) {
    System.out.println(hangmanPicture);
    System.out.println("Попыток осталось: " + attempts);
    System.out.println(word);
    System.out.println(guess);
}
```
Дальше не отходя от кассы мы видим, что числа совпадают: case 0, hangmanPicture0 и steps. Наводит на мысль, что switch case не нужен и можно картинки человечка положить в массив.

```java
// Хорошо
private String[] hangmanPictures = {
            """
                            +---+
                            |   |
                            O   |
                           /|\\  |
                           / \\  |
                                |
                        ________|
                        """,
            """
                            +---+
                            |   |
                            O   |
                           /|\\  |
                           /    |
                                |
                        ________|
                        """,
            """
                            +---+
                            |   |
                            O   |
                           /|\\  |
                                |
                                |
                        ________|
                        """,
            """
                            +---+
                            |   |
                            O   |
                           /|   |
                                |
                                |
                        ________|
                        """,
            """
                            +---+
                            |   |
                            O   |
                            |   |
                                |
                                |
                        ________|
                        """,
            """
                            +---+
                            |   |
                            O   |
                                |
                                |
                                |
                        ________|
                        """,
            """
                            +---+
                            |   |
                                |
                                |
                                |
                                |
                        ________|
                        """
    };
    public void DrawGallows(){
        draw(hangmanPictures[steps], steps); // Как элегантно получилось, не правда ли?
    }

    private void draw(String hangmanPicture, int attempts) {
        System.out.println(hangmanPicture);
        System.out.println("Попыток осталось: " + attempts);
        System.out.println(word);
        System.out.println(guess);
    }
```

## Generate
Утильный класс для считывания слов из файла. По возможности не делай статичные методы. И класс делает не то, что следует из его названия. Посмотрев на этот класс, у читающего может возникнуть чувство, что класс как-то необычно генерирует слова, но на деле это просто считывание из файла. У меня все.
# Выводы
## Минусы
1. ООП не понят
2. Есть подозрения, что перешел из другого языка. Если хочешь переходить всерьез и надолго, то не следует перенебрегать правилами нового языка. Меняй привычки. Стоит почитать конвенции по именованию всего.

## Плюсы
1. Проект доделан. Для новичка это важно. Со стороны может прозвучать не так классно, но на деле это очень важно. У меня много недоделанных проектов и жалею о том и немного обидно за это. Если еще и симуляцию сделаешь, скинешь на ревью и отрефачишь, то будет бомба! Только не зазнавайся, тебе еще исправить и отрефачить виселицу, хехе.
