# Refactoring

## WordCount

From ZEZAY's Readability code: https://github.com/ZEZAY/Penguin-Space

---

In the `GameViewManager.java` class

https://github.com/ZEZAY/Penguin-Space/blob/master/src/view/GameViewManager.java

### initializeStage() Method

consider this code:

```java
private void initializeStage() {
    gamePane = new AnchorPane();
    gameScene = new Scene(gamePane, Double.parseDouble(property.getproperty("game.width")), Double.parseDouble(property.getproperty("game.height")));
    gameStage = new Stage();
    gameStage.setScene(gameScene);
}
```

- Refactoring Signs:

  - Parameter in line 3 is too long.

- Refactoring: use another format or add variables

```java
private void initializeStage() {
    gamePane = new AnchorPane();
    gameScene = new Scene(
        gamePane,
        Double.parseDouble(property.getproperty("game.width")),
        Double.parseDouble(property.getproperty("game.height"))
    );
    gameStage = new Stage();
    gameStage.setScene(gameScene);
}
```

### createGameElements() Method

consider this code:

```java
// create grey meteors
greyMeteors = new ImageView[3];
for (int i = 0; i < greyMeteors.length; i++) {
    greyMeteors[i] = new ImageView(property.getproperty("game.meteor_grey"));
    setGameElementNewPosition(greyMeteors[i]);
    gamePane.getChildren().add(greyMeteors[i]);

// create brown meteors
brownMeteors = new ImageView[3];
for (int i = 0; i < brownMeteors.length; i++) {
    brownMeteors[i] = new ImageView(property.getproperty("game.meteor_brown"));
    setGameElementNewPosition(brownMeteors[i]);
    gamePane.getChildren().add(brownMeteors[i]);
}
```

- Refactoring Signs:

  - This method is a long method.
  - It also use duplicate code.

- Refactoring: create a new method for the duplicate code

```java
private void createMeteors(string meteorColor) {
    meteors = new ImageView[3];
    for (int i = 0; i < meteors.length; i++) {
        meteors[i] = new ImageView(property.getproperty(meteorColor));
        setGameElementNewPosition(meteors[i]);
        gamePane.getChildren().add(meteors[i]);
    }
}
createMeteors("game.meteor_brown")
createMeteors("game.meteor_grey")
```

### moveBackground() Method

consider this code:

```java
private void moveBackground() {
    gridPane1.setLayoutY(gridPane1.getLayoutY() + 0.5);
    gridPane2.setLayoutY(gridPane2.getLayoutY() + 0.5);
    if (gridPane1.getLayoutY() >= 1024)
        gridPane1.setLayoutY(-1024);
    if (gridPane2.getLayoutY() >= 1024)
        gridPane2.setLayoutY(-1024);
}
```

- Refactoring Signs:

  - Duplicate number and code

- Refactoring: use loop, global variable

```java
private int bgSlide = 0.5;
private int bgCheck = 1024;
private void moveBackground() {
    for (Pane gridPane: [gridPane1, gridPane2]) {
        gridPane.setLayoutY(gridPane1.getLayoutY() + bgSlide);
        if (gridPane.getLayoutY() >= bgCheck)
            gridPane.setLayoutY(-bgCheck);
    }
}
```

### moveGameElement() Method

consider this code:

```java
private void moveGameElement() {
    // move star
    star.setLayoutY(star.getLayoutY() + 5);
    if (star.getLayoutY() > 900)
        setGameElementNewPosition(star);
    // move words
    for (Text word : wordList) {
        word.setLayoutY(word.getLayoutY() + 2);
        if (word.getLayoutY() > 900)
            setWordNewPosition(word);
    }
    // move meteors
    for (ImageView meteor : greyMeteors) {
        meteor.setLayoutY(meteor.getLayoutY() + 7);
        meteor.setRotate(meteor.getRotate() + 4);
        if (meteor.getLayoutY() > 900)
            setGameElementNewPosition(meteor);
    }
    for (ImageView meteor : brownMeteors) {
        meteor.setLayoutY(meteor.getLayoutY() + 7);
        meteor.setRotate(meteor.getRotate() + 4);
        if (meteor.getLayoutY() > 900)
            setGameElementNewPosition(meteor);
    }
}
```

- Refactoring Signs:

  - Same as createGameElements() and moveBackground() methods
  - Duplicate number and code

- Coding Style Error:

  - has space before ':'

- Refactoring: create enum class then use loop and global variable

```java
// contain star, words, meteors as Item objects
private List<Item> gameElements;
private int itemCheck = 900;

private void moveGameElement() {
    for (Item item: gameElements) {
        // slideY, rotate are in Item Enum class
        item.setLayoutY(item.getLayoutY() + item.slideY);
        item.setRotate(item.getRotate() + item.rotate);
        if (item.getLayoutY() > itemCheck) {
            // another new method to move Item object
            setItemNewPosition(item);
        }
    }
}
```

### setGameElementNewPosition() and setWordNewPosition Methods

consider this code:

```java
private void setGameElementNewPosition(ImageView item) {
    item.setLayoutX(randomPositionGenerators.nextInt(370));
    item.setLayoutY(-(randomPositionGenerators.nextInt(3200) + 600));
}
private void setWordNewPosition(Text txt) {
    txt.setLayoutX(randomPositionGenerators.nextInt(370));
    txt.setLayoutY(-(randomPositionGenerators.nextInt(3200) + 600));
}
```

- Refactoring Signs:

  - Very much the same as moveGameElement() method
  - Duplicate number and code

- Refactoring: create enum class and replace methods with new method

```java
private void setItemNewPosition(Item item) {
    item.setLayoutX(randomPositionGenerators.nextInt(370));
    item.setLayoutY(-(randomPositionGenerators.nextInt(3200) + 600));
}
```

### calculateDistance() Method

consider this code:

```java
private double calculateDistance(ImageView ship, ImageView item, double needX, double needY) {
    double x1 = ship.getLayoutX() + 40;
    double x2 = item.getLayoutX() + needX;
    double y1 = ship.getLayoutY() + 30;
    double y2 = item.getLayoutY() + needY;
    return Math.sqrt(Math.pow(x1 - x2, 2) + Math.pow(y1 - y2, 2));
}
```

- Refactoring Signs:

  - number 40, needX, and needY is too much like hard code.

- Refactoring: create add image size to SHIP enum class and Item enum class that we create above

```java
private double calculateDistance(ImageView ship, Item item) {
    double x1 = ship.getLayoutX() + ship.sizeWidth;
    double x2 = item.getLayoutX() + item.extraX;
    double y1 = ship.getLayoutY() + ship.sizeHight;
    double y2 = item.getLayoutY() + item.extraY;
    return Math.sqrt(Math.pow(x1 - x2, 2) + Math.pow(y1 - y2, 2));
}
```
