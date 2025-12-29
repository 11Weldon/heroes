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



Написанный код 
package programs;

import com.battle.heroes.army.Army;
import com.battle.heroes.army.Unit;
import com.battle.heroes.army.programs.GeneratePreset;

import java.util.*;

public class GeneratePresetImpl implements GeneratePreset {

    private static final int MAX_SAME_TYPE = 11;
    private static final int MAX_ROWS = 3;   // три ряда
    private static final int MAX_COLS = 9;   // максимум юнитов в ряду
    private static final int START_X = 24;   // правая сторона поля

    @Override
    public Army generate(List<Unit> unitList, int maxPoints) {
        List<Unit> result = new ArrayList<>();
        Map<String, Integer> typeCount = new HashMap<>();
        Map<String, Integer> typeIndex = new HashMap<>();
        int totalPoints = 0;

        // сортировка по эффективности: (атака + здоровье) / стоимость
        unitList.sort((a, b) -> Double.compare(
                (b.getBaseAttack() + b.getHealth()) / (double) b.getCost(),
                (a.getBaseAttack() + a.getHealth()) / (double) a.getCost()
        ));

        int row = 0;
        int col = 0;

        while (true) {
            boolean added = false;

            for (Unit template : unitList) {
                String type = template.getUnitType();
                int count = typeCount.getOrDefault(type, 0);

                if (count >= MAX_SAME_TYPE) continue;
                if (totalPoints + template.getCost() > maxPoints) continue;

                // создаём новый юнит с уникальным именем
                int index = typeIndex.getOrDefault(type, 0) + 1;
                Unit unit = new Unit(
                        template.getName() + " " + index,
                        type,
                        template.getHealth(),
                        template.getBaseAttack(),
                        template.getCost(),
                        template.getAttackType(),
                        template.getAttackBonuses(),
                        template.getDefenceBonuses(),
                        START_X + row,  // x
                        col             // y
                );

                unit.setProgram(template.getProgram());

                result.add(unit);
                totalPoints += template.getCost();
                typeCount.put(type, count + 1);
                typeIndex.put(type, index);
                added = true;

                // обновляем координаты
                col++;
                if (col >= MAX_COLS) {
                    col = 0;
                    row++;
                    if (row >= MAX_ROWS) row = 0;
                }
            }

            if (!added) break;
        }

        Army army = new Army(result);
        army.setPoints(totalPoints);
        return army;
    }
}

package programs;

import com.battle.heroes.army.Army;
import com.battle.heroes.army.Unit;
import com.battle.heroes.army.programs.PrintBattleLog;
import com.battle.heroes.army.programs.SimulateBattle;

import java.util.*;

public class SimulateBattleImpl implements SimulateBattle {
    
    private PrintBattleLog printBattleLog;
    
    public SimulateBattleImpl() {}

    @Override
    public void simulate(Army playerArmy, Army computerArmy) throws InterruptedException {

        while (hasAlive(playerArmy) && hasAlive(computerArmy)) {

            List<Unit> order = new ArrayList<>();
            order.addAll(playerArmy.getUnits());
            order.addAll(computerArmy.getUnits());

            order.removeIf(u -> !u.isAlive());

            order.sort(Comparator.comparingInt(Unit::getBaseAttack).reversed());

            for (Unit unit : order) {
                if (!unit.isAlive()) continue;

                // ⚠️ логирование происходит внутри attack()
                unit.getProgram().attack();
            }
        }
    }

    private boolean hasAlive(Army army) {
        for (Unit u : army.getUnits()) {
            if (u.isAlive()) return true;
        }
        return false;
    }
}
package programs;

import com.battle.heroes.army.Unit;
import com.battle.heroes.army.programs.SuitableForAttackUnitsFinder;

import java.util.*;

public class SuitableForAttackUnitsFinderImpl implements SuitableForAttackUnitsFinder {

    @Override
    public List<Unit> getSuitableUnits(List<List<Unit>> unitsByRow, boolean isLeftArmyTarget) {
        List<Unit> result = new ArrayList<>();

        int rowIndex = isLeftArmyTarget
                ? findFrontRowFromLeft(unitsByRow)
                : findFrontRowFromRight(unitsByRow);

        if (rowIndex == -1) return result;

        for (Unit unit : unitsByRow.get(rowIndex)) {
            if (unit.isAlive()) {
                result.add(unit);
            }
        }

        return result;
    }

    private int findFrontRowFromLeft(List<List<Unit>> rows) {
        for (int i = 0; i < rows.size(); i++) {
            if (!rows.get(i).isEmpty()) return i;
        }
        return -1;
    }

    private int findFrontRowFromRight(List<List<Unit>> rows) {
        for (int i = rows.size() - 1; i >= 0; i--) {
            if (!rows.get(i).isEmpty()) return i;
        }
        return -1;
    }
}
package programs;

import com.battle.heroes.army.Unit;
import com.battle.heroes.army.programs.Edge;
import com.battle.heroes.army.programs.UnitTargetPathFinder;

import java.util.*;

public class UnitTargetPathFinderImpl implements UnitTargetPathFinder {

    private static final int WIDTH = 27;
    private static final int HEIGHT = 21;
    private static final int[][] DIRS = {
            {1, 0}, {-1, 0}, {0, 1}, {0, -1} // только вертикально и горизонтально
    };

    @Override
    public List<Edge> getTargetPath(Unit attackUnit, Unit targetUnit, List<Unit> existingUnitList) {

        Set<String> blocked = new HashSet<>();
        for (Unit u : existingUnitList) {
            if (u.isAlive()) {
                blocked.add(key(u.getxCoordinate(), u.getyCoordinate()));
            }
        }

        int sx = attackUnit.getxCoordinate();
        int sy = attackUnit.getyCoordinate();
        int tx = targetUnit.getxCoordinate();
        int ty = targetUnit.getyCoordinate();

        Queue<int[]> queue = new LinkedList<>();
        Map<String, String> prev = new HashMap<>();

        queue.add(new int[]{sx, sy});
        blocked.remove(key(sx, sy));

        while (!queue.isEmpty()) {
            int[] p = queue.poll();
            String pk = key(p[0], p[1]);

            if (p[0] == tx && p[1] == ty) break;

            for (int[] d : DIRS) {
                int nx = p[0] + d[0];
                int ny = p[1] + d[1];

                // проверка границ поля
                if (nx < 0 || nx >= WIDTH || ny < 0 || ny >= HEIGHT) continue;

                String nk = key(nx, ny);
                if (blocked.contains(nk) || prev.containsKey(nk)) continue;

                prev.put(nk, pk);
                queue.add(new int[]{nx, ny});
            }
        }

        // восстанавливаем путь
        List<Edge> path = new ArrayList<>();
        String cur = key(tx, ty);
        if (!prev.containsKey(cur)) return path; // путь не найден

        while (!cur.equals(key(sx, sy))) {
            String[] coords = cur.split(",");
            path.add(0, new Edge(Integer.parseInt(coords[0]), Integer.parseInt(coords[1])));
            cur = prev.get(cur);
        }

        return path;
    }

    private String key(int x, int y) {
        return x + "," + y;
    }
}


