# Phone Inspection Visitor

Visitor behavioral pattern in Java: a repair-shop inspection where several unrelated
operations (repair cost, recycling value, diagnostic report) run over the parts of a
smartphone without modifying the part classes.

## Structure

- **Element interface:** `PhoneComponent` declares `accept(PhoneVisitor)`.
- **Concrete Elements (3):** `Display`, `Battery`, `Camera`. Each `accept()` calls its own
  `visitX(this)`, which is the double-dispatch step.
- **Visitor interface:** `PhoneVisitor` declares `visitDisplay`, `visitBattery`, `visitCamera`.
- **Concrete Visitors (3):** `RepairCostVisitor`, `RecyclingValueVisitor`, and
  `DiagnosticReportVisitor` (added later to demonstrate extensibility).
- **Object Structure:** `Smartphone` holds the parts and calls `accept()` on each one in `inspect(visitor)`.
- **Client:** `Main` builds a phone and runs each visitor over it.

## Clean Code principles applied

**1. Double dispatch instead of type-checking**
Before (one method choosing behaviour by type):
```java
if (component instanceof Display) { ... }
else if (component instanceof Battery) { ... }
else if (component instanceof Camera) { ... }
```
After:
```java
@Override
public void accept(PhoneVisitor visitor) {
    visitor.visitBattery(this);
}
```
The element's own type picks the `visitX` method, so there are no `instanceof` checks anywhere in the code.

**2. Names that separate Element and Visitor responsibilities**
Parts are named after what they are (`Display`, `Battery`, `PhoneComponent`). Operations are
named after what they compute (`RepairCostVisitor`, `RecyclingValueVisitor`). Reading a class
name tells you which side of the pattern it belongs to.

**3. Single Responsibility: one operation per visitor**
Each concrete visitor implements exactly one operation across all part types. Repair cost
logic, recycling logic and reporting logic never share a class.

**4. Encapsulation trade-off, stated and contained**
Visitors need to read element data, so each element exposes read-only getters
(`getHealthPercent()`, `isCracked()`). All fields are `private final` and there are no setters,
so a visitor can inspect a part but never change it.

**5. No magic
