## Servant
Design patterns Command and Servant are very similar and implementations of them are often virtually the same. The difference between them is the approach to the problem.

- For the Servant pattern we have some objects to which we want to offer some functionality. We create a class whose instances offer that functionality and which defines an interface that serviced objects must implement. Serviced instances are then passed as parameters to the servant.
- For the Command pattern we have some objects that we want to modify with some functionality. So, we define an interface which commands which desired functionality must be implemented. Instances of those commands are then passed to original objects as parameters of their methods.

It is defined as a behavioral design pattern, because the servant is acting on the classes to change their behavior.

```csharp 
public class MoveServant
{
    // Method, which will move Movable implementing class to position where
    public void MoveTo(IMovable serviced, Position where)
    {
        // Do some other stuff to ensure it moves smoothly and nicely, this is
        // the place to offer the functionality
        serviced.SetPosition(where);
    }

    // Method, which will move Movable implementing class by dx and dy
    public void MoveBy(IMovable serviced, int dx, int dy)
    {
        dx += serviced.GetPosition().XPosition;
        dy += serviced.GetPosition().YPosition;
        serviced.SetPosition(new Position(dx, dy));
    }
}

// Interface specifying what serviced classes needs to implement, to be
// serviced by servant.
public interface IMovable
{
    public void SetPosition(Position p);

    public Position GetPosition();
}

public class Triangle : IMovable
{
    private Position _p;

    public void SetPosition(Position p)
    {
        this._p = p;
    }

    public Position GetPosition()
    {
        return this._p;
    }
}

public class Ellipse : IMovable
{
    private Position _p;

    public void SetPosition(Position p)
    {
        this._p = p;
    }

    public Position GetPosition()
    {
        return this._p;
    }
}

public class Rectangle : IMovable
{
    private Position _p;

    public void SetPosition(Position p)
    {
        this._p = p;
    }

    public Position GetPosition()
    {
        return this._p;
    }
}

public class Position
{
    public int XPosition;
    public int YPosition;

    public Position(int dx, int dy)
    {
        XPosition = dx;
        YPosition = dy;
    }
}
```