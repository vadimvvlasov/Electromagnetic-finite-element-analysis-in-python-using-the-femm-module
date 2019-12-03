# Electromagnetic finite element analysis in python with the femm module

## 1. Goal for the project
- выбор оптимальных параметров катушек оповещателей для устаноки на внутритрубные дефектоскопы трубопроводов диаметром: 219 мм, 273 мм, 325 мм, 377 мм, 426 мм, 500 мм, 700 мм.
![Object Dagram](ObjectDiagram.jpeg)

## 2. Details about contribution for the project

- проектирование и разработка библиотеки классов для моделирования катушек оповещателей в составе внутритрубных дефектоскопов
- тестирование методов классов на соответсие аналитическим методам решения
- эмпирическая проверка численных моделей (методов класса оповещателе)
- разработка критериев эффективности: напряжённомсть магнитного поля, мощность активных потерь
- параметрических расчёт оповещателей и выбор оптимальных параметров.

## 3. An overview of the project

![Object for Low freq EM analisys](OZO500/pic/OZO-500-273.jpg)

![Low freq hfrmonic EM analisys](OZO500/pic/OZO-500-273_FEM.jpg)

## Проектирование классов
![UML Class Dagram](UMLClassDiagram.jpg)
###
```python
# coding=utf-8
import femm
from math import pi, sqrt, exp

# Диаметр провода выбирается из ряда diameterList
diameterList = [0.063, 0.071, 0.08, 0.09, 0.1, 0.112, 0.12, 0.125, 0.13, 0.14, 0.15, 0.16, 0.17, 0.18, 0.19, 0.2, 0.21,
                0.224, 0.236, 0.25, 0.265, 0.28, 0.3, 0.315, 0.335, 0.355, 0.38, 0.4, 0.425, 0.475, 0.45, 0.5, 0.53,
                0.56, 0.6, 0.63, 0.67, 0.69, 0.71, 0.75, 0.77, 0.8, 0.83, 0.85, 0.9, 0.93, 0.95, 1, 1.06, 1.12, 1.08,
                1.18, 1.25, 1.32, 1.4, 1.45, 1.5, 1.56, 1.6, 1.7, 1.8, 1.9, 2, 2.12, 2.24, 2.36, 2.44, 2.5]
```
### class Wire
```python
class Wire(object):
    '''
    Создание объекта Провод
    '''

    version = '0.1'  # class variable

    def __init__(self, diameter):
        '''
        Initialize Wire Diameter in meters
        '''
        self.diameter = diameter

    def area(self):
        '''
        Calculate wire Cross-sectional Area
        '''
        return pi * self.diameter ** 2 / 4
```
### class Coil 
```python
class Coil(object):
    def __init__(self, outerDiameter, innerDiameter, length, wireDiameter, fillFactor=57.3 / 100):
        '''
        Objects Inicialisation for Wire and Coil
        '''
        self.outerDiameter = outerDiameter
        self.innerDiameter = innerDiameter
        self.length = length
        self.fillFactor = fillFactor
        self.Wire = Wire(wireDiameter)

    def area(self):
        '''
        Calculate coil cross-section area
        '''
        return (self.outerDiameter - self.innerDiameter) / 2 * self.length

    def windingNomber(self):
        '''
        Calculate winding nomber in Coil
        '''
        return int(self.fillFactor * self.area() / self.Wire.area())

    def wireLength(self):
        '''
        Calculate wire length in Coil
        '''
        return pi * self.windingNomber() * (self.outerDiameter + self.innerDiameter) / 2

    def resistanceAnalitical(self):
        """ Аналитический расчёт сопротивления катушки """
        rho20 = 0.0172e-6  # (Ohm*mm^2)/m
        return rho20 * self.wireLength() / self.Wire.area()
```

## Resistance and Inductance as a function of frequnecy. Сalculation and measurement
![measurement](IMG_20191203_162938_HDR.jpg)
![Сalculation](OZO-154-426_1.18_verif.png)
