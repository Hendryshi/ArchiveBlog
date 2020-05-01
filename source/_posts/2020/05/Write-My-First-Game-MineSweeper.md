---
title: 'Write My First Game: MineSweeper'
permalink: 'Write-My-First-Game-MineSweeper'
toc: true
thumbnail: /2020/05/01/Write-My-First-Game-MineSweeper/cover.png
tags:
    - C#
    - WinForm
categories:
    - Develop
    - Project

date: 2020-05-01 00:00:00
update: 2020-05-01 00:00:00
---

This is my first game developed by C# WinForm platform. The initial object is to practice my C# level. So the first game jump into my mind is "MineSweeper". It is enough simple and interesting. In this post I will list my conception and some difficult point during my development.
<!-- more -->

**Welcome to see my [project](https://github.com/Hendryshi/MineSweeper) in github.**

**See also [The rule for mineSweeper](http://www.freeminesweeper.org/help/minehelpinstructions.html)**


# Class Conception

<!-- classDiagram
class Game{
    +Square[ , ] squares
    +gameLevel
    +isStart
    +OpenSingleSq()
    +OpenAroundSq()
    +CheckWinLose()
}
class Square{
    +status
    +value
    +position
    +GetAroundSquare()
    +AddRemoveFlag()
}
class Frame{
    +Bitmap bufferMainFrame
	+private Bitmap bufferInfoFrame
	+private Bitmap bufferMineFrame
    +DrawMainFrame()
    +DrawMineFrame()
}
Game *-- Square
Game *-- Frame -->


![](class.png)

I used globally three objects in models: 
- Class `Game` presents one game, every time when we create or restart a new game, this class is recreated. Through this class we control the results of game, and some other properties like `flagCounts` or `gameLevel`.

- Class `Square` presents every small square on the board, it is applied to a two-dimension array in class `Game`. In this class, I used an enumeration to define the status of square(open, close, flagged...). The value of squares (-1 for mined, and [0-9] for others), and position in the boards is also listed in this class.

- Class `Frame` presents the board on the interface. I used this class to do every drawing function. every drawing function return a temporary bitmap picture for the mainForm to apply on the interface.



# Program process

<!--  
  graph LR
  
  A(MouseDown)
  B{L, R or Two}
  C(MouseClick)
  D[Add flag]
  E(Open Square)
  F{Hit Mine or Win}
  G([Lose])
  H(MouseUp)
  I(Open around square)
  J([Win])
  style E fill: #FFF333
  style I fill: #FFF333
  style B fill: #9999FF
  style F fill: #9999FF
-->
[comment]: # (
  A --> B
  B -->|L| C
  B -->|Two| H
  H --> I --> F
  C --> E --> F
  E -->|Recursion| E
  I -->|Recursion| I
  F -->|Hit Mine| G
  F -->|Continue| A
  F -->|Win| J
  B -->|Right| D --> A)

![](process.png)

As described above, every time we click our mouse, the program enter into different operation defined in object `Game` according to different mouse event (left click, right click or both click). And at the end of every function, we use the object `Frame` to redraw the board and reflect to the interface.

# Some focal points in my development

## Different mouse events

In the program, I use many different mouse events. I'd like to list as below by calling order. Note that to save the status of my left click and right click, I use two properties `leftDown` and `rightDown` in my object winForm.

- **MouseMove event**: This function is called when your mouse are moving 
    - As I want to realise that if I hold clicking my left mouse and when moving through the board, all the squares will change their status (like a button is pressed), this event is called. 
    ```csharp
    private void pnlMine_MouseMove(object sender, MouseEventArgs e)
    {
        if(game.InGameSize(e.Location) && !game.Result.HasValue)
        {
            if(leftDown)
            {
                game.SetSquaresDown(e.Location, rightDown);
                RefreshFrame();
            }
        }
    }
  ```

- **MouseDown event**: This function is called when your mouse is click down
    - I use this function to add my flag if it's the right click, also if left click, I will set the property `leftDown` to be true and use it in mouseUp function.

    ```csharp
    private void pnlMine_MouseDown(object sender, MouseEventArgs e)
    {
        if(!game.Result.HasValue)
        {
            switch(e.Button)
            {
                case MouseButtons.Left:
                    leftDown = true;
                    game.SetSquaresDown(e.Location, rightDown);
                    game.ChangeFace(GameFace.MouthOpen);
                    break;
                case MouseButtons.Right:
                    rightDown = true;
                    if(!leftDown)
                        game.AddRemoveFlag(e.Location);
                    break;
            }

            if(leftDown && rightDown && game.InGameSize(e.Location))
                game.SetSquaresDown(e.Location, true);

            RefreshFrame();
        }
    }
    ```

- **MouseClick event**: This function is called after function MouseDown
    - This function is the most common mouse event, I use it to open a single square if it's left click.

    ```Csharp
    private void pnlMine_MouseClick(object sender, MouseEventArgs e)
    {
        if(e.Button == MouseButtons.Left && (!leftDown || !rightDown) && game.InGameSize(e.Location) && !game.Result.HasValue)
        {
            if(!game.IsStart)
            {
                game.StartGame(e.Location);
                threadTimer.Change(0, 1000);
            }

            game.OpenSingleSquare(e.Location);
            RefreshFrame();
        }
    }
    ```
- **MouseUp event**: This function is called the moment when your mouse is click up (the position of click up). 
    - I use this function to open around squares if it's both click (leftDown && rightDown)

    ```Csharp
    private void pnlMine_MouseUp(object sender, MouseEventArgs e)
    {
        if(!game.Result.HasValue)
        {
            if(leftDown && rightDown && game.InGameSize(e.Location))
                game.OpenAroundSquares(e.Location);

            switch(e.Button)
            {
                case MouseButtons.Left:
                    leftDown = false;
                    break;
                case MouseButtons.Right:
                    rightDown = false;
                    break;
            }

            game.SetAllSquaresUp();
            game.ChangeFace(GameFace.SmileUp);
            RefreshFrame();
        }
    }
    ```

## The drawing jobs

As this is my first winForm application, I know few about the windows GDI+ technology, however the drawing of my mainForm is simple enough. As for the mine panel and the information panel I just use the existing picture to cover on my board.

- **Create the frame**:

    ![](frame.png)

    - When the game is started, the first thing we do is to create the entire frame, To realize that, I created three empty bitmap for the temporary buffer image (one for info panel, one for mine panel, and one for main panel) in the frame object.

        ```csharp
        bufferMainFrame = new Bitmap(rctGameField.Width, rctGameField.Height);
        bufferInfoFrame = new Bitmap(rctPnlInfo.Width, rctPnlInfo.Height);
        bufferTimerFrame = new Bitmap(rctPnlTimer.Width, rctPnlTimer.Height);
        bufferMineFrame = new Bitmap(rctPnlMine.Width, rctPnlMine.Height);

        Graphics.FromImage(bufferMainFrame).Clear(GRAY);
        Graphics.FromImage(bufferInfoFrame).Clear(GRAY);
        Graphics.FromImage(bufferTimerFrame).Clear(GRAY);
        Graphics.FromImage(bufferMineFrame).Clear(GRAY);
        ```
- **Draw the squares**:
    - To draw the squares on the board, it's simple, you just need to change the image on the right position of the board and draw it on your temporary bitmap. The more difficult one is drawing the main frame, as you need to calculate every position and style. Below is the function to draw the square.

        ```csharp
        public Bitmap DrawSquare(Square square)
        {
            Graphics g = Graphics.FromImage(bufferMineFrame);
            int srcY = (int)square.Status + (square.Status == MineStatus.OpenedNumber ? (Square.MaxSquareNum - square.Value) * ImgMineUnitWidth : 0);
            Rectangle mineRect = new Rectangle(square.Location, new Size(squareSize, squareSize));
            Rectangle srcRect = new Rectangle(new Point(0, srcY), new Size(ImgMineUnitWidth, ImgMineUnitWidth));

            GraphicsUnit units = GraphicsUnit.Pixel;
            g.DrawImage(imgMine, mineRect, srcRect, units);

            return bufferMineFrame;
        }
        ```
    - The `graphics` g is to get the pen of the image, and the function `g.DrawImage` is used to draw your image `imgMine`. Note that the parameter `srcRect` is the part of image you choose in your image `imgMine`.   


- **Refresh the square on the board**:
    - As some of you may ask why you have to draw on the temporary bitmap and covering it to the real panel ? why we can't draw the image directly in the real panel?
    - There are two reasons: the first is that in my opinion the logical to draw and refresh the image need to be done in the backend, not in the controls directly. And the second and more important reason is that if we draw the image directly in real panel, that will cause a flash effect when many squares are updated in one time. As it is drawn one by one, though the interval time is short, we will also see the flash and that's not good. So we choose to draw all the squares in a temporary image and cover it to the real image only one time. 

        ```csharp The function to covering the temporary image
        private void RefreshFrame()
        {
            pnlMine.CreateGraphics().DrawImage(game.GameFrame.MineFrame, ClientRectangle.Location);
            pnlInfo.CreateGraphics().DrawImage(game.GameFrame.InfoFrame, ClientRectangle.Location);
        }
        ```

## The function to open the square

- There are two options to open the squares, one is to left click a closed square, the other is to both click an opened square if the around squares are all flagged according to the value of this square (sqValue - flagArround = 0). These two options are both called a recursion function. The algorithm is if the value of square is 0, you should traverse the 8 around squares and do the same verification for the 8 squares. 

```csharp The recursion function
public bool ExpandSquares(Square sq)
{
    bool noError = true;
    List<Square> lstAroundSquare = sq.GetAroundSquare(squares, false, true);

    foreach(Square sqAround in lstAroundSquare)
    {
        noError &= sqAround.OpenSquare();
        gameFrame.DrawSquare(sqAround);

        if(sqAround.Value == 0)
            noError &= ExpandSquares(sqAround);
    }
    return noError;
}
```

```csharp The function to get the around squares according different condition
public List<Square> GetAroundSquare(Square[,] squares, bool excludeMine = false, bool excludeOpen = false)
{
    var query = from Square sq in squares 
                where sq.location.X >= this.location.X - squareSize && sq.location.X <= this.location.X + squareSize
                && sq.location.Y >= this.location.Y - squareSize && sq.location.Y <= this.location.Y + squareSize
                && sq.location != this.location
                select sq;
    if(excludeMine)
        query = query.ToList().Where(sq => !sq.IsMine());

    if(excludeOpen)
        query = query.ToList().Where(sq => sq.IsClosed());

    return query.ToList();
}
```

## Yates shuffle Algorithm

- This algorithm is to set the mines randomly in the board (99 mines of 480 squares). The theory of the algorithm is that I switch my two-dimension array to one dimension, and I suppose that the first 99 squares are mines. Then I do the boucle from the last one to the fist item. I switch the last item with randomly first n-1 items. Do it for n-1 times. 
- The difficulty is that the first click of a game can't be a mine. So that change my algorithm. I cannot set my mine when creating my object Game, but should set all the mines after the first click on the board. Moreover, the click square should only be 0 and it can open a block of squares around it (the around square should not be mine). To solve this problem, I set the first 99 squares as mine when creating game object, and after the first click, I calculate how many mines around the first square (include itself), and add the number of mine to the squares after 99.

    ```csharp The function of Yates shuffle
    private void KnuthShuffleMine(Point point)
        {
            Random ran = new Random();
            int indexX = point.X / squareSize;
            int indexY = point.Y / squareSize;
            Square sqPoint = squares[indexX, indexY];

            int indexOffset = sqPoint.GetAroundMineCount(squares) + (sqPoint.IsMine() ? 1 : 0);

            for(int y = 0; y <= squares.GetLength(1) - 1; y++)
            {
                for(int x = 0; x <= squares.GetLength(0) - 1; x++)
                {
                    // this area cannot be mine
                    if(x >= indexX - 1 && x <= indexX + 1 && y >= indexY - 1 && y <= indexY + 1)
                        squares[x, y].Value = 0;
                    else
                    {
                        Point ranP = GetRandomPoint(ran, x, y, indexX, indexY);
                        int ranSquareValue = ranP.Y * squares.GetLength(0) + ranP.X < mineCount + indexOffset ? -1 : squares[ranP.X, ranP.Y].Value;

                        int value = y * squares.GetLength(0) + x < mineCount + indexOffset ? -1 : squares[x, y].Value;
                        squares[x, y].Value = ranSquareValue;
                        squares[ranP.X, ranP.Y].Value = value;
                        gameFrame.DrawSquare(squares[x, y]);
                    }
                }
            }

            var queryMine = from Square sq in squares
                            where sq.IsMine()
                            select sq;

            foreach(Square sq in queryMine)
            {
                List<Square> lstAroundSquare = sq.GetAroundSquare(squares, true);
                foreach(Square sqAround in lstAroundSquare)
                {
                    sqAround.Value += 1;
                    gameFrame.DrawSquare(sqAround);
                }
            }
        }
    ```

    ```csharp The function to get random point
    private Point GetRandomPoint(Random random, int x, int y, int indexX, int indexY)
    {
        int ranX;
        int ranY;
        int ranT;
        do
        {
            ranT = random.Next(y * squares.GetLength(0) + x, squares.GetLength(0) * squares.GetLength(1) - 1);
            ranX = ranT % squares.GetLength(0);
            ranY = ranT / squares.GetLength(0);
        } while(ranX >= indexX - 1 && ranX <= indexX + 1 && ranY >= indexY - 1 && ranY <= indexY + 1);

        return new Point(ranX, ranY);
    }
    ```


## ThreadTimer function

Also, in my game I need a timer to record my grades. And change the time on the panel every seconds. 

```csharp When create a new game
if(threadTimer != null)
    threadTimer.Dispose();

threadTimer = new System.Threading.Timer(new TimerCallback(ChangeTime), null, Timeout.Infinite, 1000);
```

```csharp
private void ChangeTime(object value)
{
    if(!game.Result.HasValue)
    {
        game.ChangeTime();
        pnlTimer.CreateGraphics().DrawImage(game.GameFrame.TimerFrame, ClientRectangle.Location);
    }
    else
    {
        threadTimer.Dispose();
        if(game.Result == true && game.CheckBreakRecord())
        {
            MyInvoke mi = new MyInvoke(SetNewRecords);
            BeginInvoke(mi);
        }
    }
}
```

## Save my grades when breaking records
To save my grades (I can see it when reopening my game), I use the property of userSetting. It is different from appSetting. AppSetting cannot be modified in the program, but userSetting you can change it or load it in your program.

```csharp 
private void SetNewRecords()
{
    if(level == GameLevel.Beginner)
    {
        this.rankBegItem.Text = "Beginner: " + game.TimeRecord;
        Properties.Settings.Default["BegRecord"] = game.TimeRecord;
    }
    else if(level == GameLevel.Intermediate)
    {
        this.rankInterItem.Text = "Intermediate: " + game.TimeRecord;
        Properties.Settings.Default["InterRecord"] = game.TimeRecord;
    }
    else if(level == GameLevel.Expert)
    {
        this.rankExpertItem.Text = "Expert: " + game.TimeRecord;
        Properties.Settings.Default["ExpertRecord"] = game.TimeRecord;
    }
    Properties.Settings.Default.Save();
    MessageBox.Show("You have break a new records: " + game.TimeRecord);
}
```

# Conclusion
This is my first game developed by C# winForm. At first I think that it's easy to develop, but in fact I was still meet some difficulties and it took me about two weeks to finish it. In the next time I would like to note the process to create the install package for this small application and make it possible to install in any computer.