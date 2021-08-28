# The warehouse

## Relational Schema

![Relation schema](https://upload.wikimedia.org/wikipedia/commons/4/47/Sql_warehouse.png)

## Table creation code
```sql
CREATE TABLE Warehouses
(
    Code     INTEGER PRIMARY KEY NOT NULL,
    Location TEXT                NOT NULL,
    Capacity INTEGER             NOT NULL
);

CREATE TABLE Boxes
(
    Code      TEXT PRIMARY KEY NOT NULL,
    Contents  TEXT             NOT NULL,
    Value     REAL             NOT NULL,
    Warehouse INTEGER          NOT NULL,
    CONSTRAINT fk_Warehouses_Code FOREIGN KEY (Warehouse) REFERENCES Warehouses (Code)
);
```

## Sample dataset
```sql
INSERT INTO Warehouses(Code, Location, Capacity)
VALUES (1, 'Chicago', 3);
INSERT INTO Warehouses(Code, Location, Capacity)
VALUES (2, 'Chicago', 4);
INSERT INTO Warehouses(Code, Location, Capacity)
VALUES (3, 'New York', 7);
INSERT INTO Warehouses(Code, Location, Capacity)
VALUES (4, 'Los Angeles', 2);
INSERT INTO Warehouses(Code, Location, Capacity)
VALUES (5, 'San Francisco', 8);

INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('0MN7', 'Rocks', 180, 3);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('4H8P', 'Rocks', 250, 1);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('4RT3', 'Scissors', 190, 4);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('7G3H', 'Rocks', 200, 1);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('8JN6', 'Papers', 75, 1);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('8Y6U', 'Papers', 50, 3);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('9J6F', 'Papers', 175, 2);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('LL08', 'Rocks', 140, 4);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('P0H6', 'Scissors', 125, 1);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('P2T6', 'Scissors', 150, 2);
INSERT INTO Boxes(Code, Contents, Value, Warehouse)
VALUES ('TU55', 'Papers', 90, 5);
```

## Exercises
1. Select all warehouses.
2. Select all boxes with a value larger than $150.
3. Select all distinct contents in all the boxes.
4. Select the average value of all the boxes.
5. Select the warehouse code and the average value of the boxes in each warehouse.
6. Same as previous exercise, but select only those warehouses where the average value of the boxes is greater than 150.
7. Select the code of each box, along with the name of the city the box is located in.
8. Select the warehouse codes, along with the number of boxes in each warehouse. Optionally, take into account that some warehouses are empty (i.e., the box count should show up as zero, instead of omitting the warehouse from the result).
9. Select the codes of all warehouses that are saturated (a warehouse is saturated if the number of boxes in it is larger than the warehouse's capacity).
10. Select the codes of all the boxes located in Chicago.
11. Create a new warehouse in New York with a capacity for 3 boxes.
12. Create a new box, with code "H5RT", containing "Papers" with a value of $200, and located in warehouse 2.
13. Reduce the value of all boxes by 15%.
14. Apply a 20% value reduction to boxes with a value larger than the average value of all the boxes.
15. Remove all boxes with a value lower than $100.
16. Remove all boxes from saturated warehouses.

## Answers
```sql
 1.  SELECT * 
     FROM Warehouses;
```
```sql
 2. SELECT *
    FROM Boxes
    WHERE Value > 150;
```
```sql
 3. SELECT DISTINCT Contents
    FROM Boxes;
```
```sql
 4.  SELECT AVG(Value) 
     FROM Boxes;
```
```sql
 5. SELECT Warehouse, AVG(Value)
    FROM Boxes
    GROUP BY Warehouse;
```
```sql
 6.  SELECT Warehouse, AVG(Value)
     FROM Boxes
     GROUP BY Warehouse
     HAVING AVG(Value) > 150;
```
```sql
 7. SELECT Boxes.Code, Location
    FROM Warehouses
             INNER JOIN Boxes
                        ON Warehouses.Code = Boxes.Warehouse;
```
```sql
 8. SELECT Warehouse, COUNT(*)
    FROM Boxes
    GROUP BY Warehouse;
```
```sql
 9. SELECT Code
    FROM Warehouses
    WHERE Capacity <
          (
              SELECT COUNT(*)
              FROM Boxes
              WHERE Warehouse = Warehouses.Code
          );
```
```sql
10. SELECT Boxes.Code
    FROM Warehouses
             RIGHT JOIN Boxes
                        ON Warehouses.Code = Boxes.Warehouse
    WHERE Location = 'Chicago';
```
```sql
11. INSERT
    INTO Warehouses
        (Location, Capacity)
    VALUES ('New York', 3);
```
```sql
12. INSERT INTO Boxes
    VALUES('H5RT','Papers',200,2);
```
```sql
13. UPDATE Boxes 
    SET Value = Value * 0.85;
```
```sql
14. UPDATE Boxes
    SET Value = Value * 0.80
    WHERE Value > (SELECT AVG(Value) FROM (SELECT * FROM Boxes) AS X);
```
```sql
15. DELETE
    FROM Boxes
    WHERE Value < 100;
```
```sql
16. DELETE
    FROM Boxes
    WHERE Warehouse IN
          (
              SELECT Code
              FROM Warehouses
              WHERE Capacity <
                    (
                        SELECT COUNT(*)
                        FROM Boxes
                        WHERE Warehouse = Warehouses.Code
                    )
          );
```
