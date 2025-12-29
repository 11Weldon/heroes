# Heroes Battle – Пошаговая стратегия с боевой системой

## О проекте

**Heroes Battle** — пошаговая стратегия, где игрок сражается с армией компьютера.  
В рамках проекта реализованы ключевые алгоритмы:

- Генерация армии противника;
- Симуляция боя между армиями;
- Отбор целей для атаки;
- Поиск кратчайшего пути между юнитами.

Проект включает исходники Java, собранный JAR-файл с реализацией алгоритмов и ресурсы игры.

**Важно:** в папке `jars/` замените `obf.jar` на `HeroesBattleImpl.jar`, чтобы игра использовала вашу реализацию.

---

## Алгоритмы

### 1. Генерация армии (`GeneratePresetImpl`)

**Метод:** `generate(List<Unit> unitList, int maxPoints)`

- Создаёт армию компьютера с учётом ограничений:
  - максимум 11 юнитов одного типа;
  - суммарная стоимость ≤ `maxPoints`;
  - каждому юниту присваивается уникальное имя (`Archer 1`, `Archer 2`…).
- Алгоритм:
  1. Сортировка юнитов по коэффициенту **эффективность = (атака + здоровье) / стоимость**.
  2. Жадное добавление юнитов в армию с проверкой ограничений.

**Сложность:**  
- Сортировка: `O(T log T)`, T — число типов юнитов;  
- Добавление: `O(T * N)`, N — число юнитов в армии.  
**Итого:** `O(T log T + T*N)`.

**Пример кода:**
```java
unitList.sort((a, b) -> Double.compare(efficiency(b), efficiency(a)));
Unit copy = new Unit(template.getName() + " " + (count + 1), ...);

2. Симуляция боя (SimulateBattleImpl)

Метод: simulate(Army playerArmy, Army computerArmy)

Юниты ходят по убыванию атаки.

Погибшие удаляются из очереди.

Бой продолжается до уничтожения одной из армий.

Сложность: O(R * U log U)

R — количество раундов, U — число живых юнитов.

Пример кода:

order.sort(Comparator.comparingInt(Unit::getBaseAttack).reversed());
for (Unit unit : order) {
    if (!unit.isAlive()) continue;
    unit.getProgram().attack();
}

2. Симуляция боя (SimulateBattleImpl)

Метод: simulate(Army playerArmy, Army computerArmy)

Юниты ходят по убыванию атаки.

Погибшие удаляются из очереди.

Бой продолжается до уничтожения одной из армий.

Сложность: O(R * U log U)

R — количество раундов, U — число живых юнитов.

Пример кода:

order.sort(Comparator.comparingInt(Unit::getBaseAttack).reversed());
for (Unit unit : order) {
    if (!unit.isAlive()) continue;
    unit.getProgram().attack();
}

2. Симуляция боя (SimulateBattleImpl)

Метод: simulate(Army playerArmy, Army computerArmy)

Юниты ходят по убыванию атаки.

Погибшие удаляются из очереди.

Бой продолжается до уничтожения одной из армий.

Сложность: O(R * U log U)

R — количество раундов, U — число живых юнитов.

Пример кода:

order.sort(Comparator.comparingInt(Unit::getBaseAttack).reversed());
for (Unit unit : order) {
    if (!unit.isAlive()) continue;
    unit.getProgram().attack();
}

Запуск игры

Поместите HeroesBattleImpl.jar в папку jars/, заменив obf.jar.

Запустите:

java -jar "Heroes Battle-1.0.0.jar"


Игра автоматически использует вашу реализацию алгоритмов.

