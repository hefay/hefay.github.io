---
layout: post
title: "Výchozí metody rozhraní v JUnit 5 jako nástroj pro kompozitní testování sdílené logiky"
date: 2026-06-12 12:00:00 +0200
---

Psaní testů pro doménovou logiku často naráží na problém opakování. Když stejnou byznysovou invariantu testujeme na několika implementacích téhož rozhraní, končíme buď duplicitním kódem, nebo abstraktními base classami s komplikovanou hierarchií. JUnit 5 ve spojení s `default` metodami Java rozhraní nabízí elegantnější cestu.

## Co jsou výchozí metody rozhraní?

Od Javy 8 mohou rozhraní obsahovat metody s tělem (modifikátor `default`). Třídy, které rozhraní implementují, tyto metody zdědí, aniž by je musely překrýt. JUnit 5 (Jupiter) tohoto mechanismu využívá k definování testovacích šablon přímo v rozhraní — a testovací engine je spouští stejně, jako by byly napsány v konkrétní testovací třídě.

## Motivace z bankovní domény

Mějme rozhraní pro výpočet úrokové sazby:

```java
public interface InterestRateCalculator {
    BigDecimal calculateRate(Account account);
}
```

A dvě implementace — standardní a zvýhodněnou:

```java
public class StandardRateCalculator implements InterestRateCalculator { ... }
public class PremiumRateCalculator implements InterestRateCalculator { ... }
```

Obě implementace musí splňovat stejné invarianty: sazba nemůže být záporná, pro stejný účet musí být vracena stejná hodnota při opakovaném volání, sazba pro spořicí účet musí být vyšší než pro běžný. Psát tyto testy dvakrát (nebo třikrát) je zbytečné.

## Řešení: testovací rozhraní s default metodami

```java
public interface InterestRateCalculatorTest {

    InterestRateCalculator createCalculator();

    @Test
    default void rateMustNotBeNegative() {
        var calculator = createCalculator();
        var account = new Account(AccountType.SAVINGS, BigDecimal.ZERO);
        assertThat(calculator.calculateRate(account)).isGreaterThanOrEqualTo(BigDecimal.ZERO);
    }

    @Test
    default void rateMustBeConsistentForSameAccount() {
        var calculator = createCalculator();
        var account = new Account(AccountType.CHECKING, BigDecimal.valueOf(1000));
        var first = calculator.calculateRate(account);
        var second = calculator.calculateRate(account);
        assertThat(first).isEqualByComparingTo(second);
    }

    @Test
    default void savingsRateMustExceedCheckingRate() {
        var calculator = createCalculator();
        var savings = new Account(AccountType.SAVINGS, BigDecimal.valueOf(5000));
        var checking = new Account(AccountType.CHECKING, BigDecimal.valueOf(5000));
        assertThat(calculator.calculateRate(savings))
                .isGreaterThan(calculator.calculateRate(checking));
    }
}
```

Konkrétní testovací třída už jen dodá tovární metodu a případné specializace:

```java
class StandardRateCalculatorTest implements InterestRateCalculatorTest {
    @Override
    public InterestRateCalculator createCalculator() {
        return new StandardRateCalculator();
    }

    // specifický test jen pro standardní kalkulačku
    @Test
    void standardRateForLowBalance() {
        var calculator = createCalculator();
        var account = new Account(AccountType.SAVINGS, BigDecimal.valueOf(100));
        assertThat(calculator.calculateRate(account))
                .isEqualByComparingTo("0.005");
    }
}

class PremiumRateCalculatorTest implements InterestRateCalculatorTest {
    @Override
    public InterestRateCalculator createCalculator() {
        return new PremiumRateCalculator();
    }
}
```

Jen implementací rozhraní `InterestRateCalculatorTest` získává `PremiumRateCalculatorTest` všechny testy ze sdíleného rozhraní — a JUnit 5 je spustí jako běžné `@Test` metody. Žádné dědičnosti, žádné base classy, žádná duplicita.

## Parametrizace a adaptace kontextu

`default` metody můžou být i parametrizované — kombinace s `@MethodSource` nebo vlastními `ArgumentsProvider` je přímočará:

```java
public interface TransactionFeeTest {

    TransactionFeeCalculator createCalculator();

    @ParameterizedTest
    @MethodSource("feeTestCases")
    default void feeShouldBeWithinLimits(Account account, BigDecimal expectedFee) {
        var fee = createCalculator().computeFee(account);
        assertThat(fee).isBetween(BigDecimal.ZERO, expectedFee);
    }

    static Stream<Arguments> feeTestCases() {
        return Stream.of(
            Arguments.of(new Account(AccountType.CHECKING, BigDecimal.valueOf(100)), BigDecimal.valueOf(5)),
            Arguments.of(new Account(AccountType.SAVINGS, BigDecimal.valueOf(1000)), BigDecimal.valueOf(2))
        );
    }
}
```

## Předávání konfigurovatelných závislostí

Pokud testovaná logika vyžaduje další objekty (např. repository, klienta třetí strany), rozhraní může definovat výchozí implementace a testovací třída je podle potřeby překryje:

```java
public interface TransferValidationTest {

    TransferValidator createValidator();

    default CurrencyConverter createCurrencyConverter() {
        return new FixedRateCurrencyConverter(BigDecimal.ONE); // testovací stub
    }

    @Test
    default void differentCurrencyTransferShouldConvertCorrectly() {
        var validator = createValidator();
        var converter = createCurrencyConverter();
        var transfer = new Transfer("CZK", "EUR", BigDecimal.valueOf(1000));
        var result = validator.validate(transfer, converter);
        assertThat(result.isValid()).isTrue();
    }
}
```

## Srovnání s alternativními přístupy

Tabulka níže shrnuje hlavní rozdíly mezi přístupy:

| Přístup | Duplicita | Flexibilita | Čitelnost | Údržba |
|---|---|---|---|---|
| Base class | Nízká (dědí se) | Omezená (jedna osa specializace) | Střední | Horší (křehká hierarchie) |
| Kopírování testů | Vysoká | Vysoká | Nízká | Špatná |
| `default` metody v rozhraní | Nízká | Vysoká (vícenásobná implementace) | Vysoká | Dobrá |
| Helper utilities | Střední | Vysoká | Střední | Dobrá |

## Ekvivalenty v jiných jazycích

Stejný pattern není omezen na Javu. Podobné mechanismy nabízí:

### Kotlin

Kotlin podporuje `default` metody v rozhraních od verze 1.0 (zkompilují se do Java `default` metod), takže pattern funguje identicky. Kotlin navíc přidává *extension functions*, které lze použít k obohacení testovacích objektů bez nutnosti implementace rozhraní:

```kotlin
interface InterestRateCalculatorTest {
    fun createCalculator(): InterestRateCalculator

    @Test
    fun `rate must not be negative`() {
        val calculator = createCalculator()
        val account = Account(AccountType.SAVINGS, BigDecimal.ZERO)
        assertDoesNotThrow { calculator.calculateRate(account) }
    }
}
```

Extension funkce umožňují přidávat testovací helpery:

```kotlin
fun InterestRateCalculator.Companion.withZeroRate() = ...
```

### C# / .NET

Od C# 8.0 obsahují rozhraní výchozí implementace metod:

```csharp
public interface IInterestRateCalculatorTest {
    IInterestRateCalculator CreateCalculator();

    [Fact]
    void RateMustNotBeNegative() {
        var calculator = CreateCalculator();
        var account = new Account(AccountType.Savings, 0m);
        Assert.True(calculator.CalculateRate(account) >= 0);
    }
}
```

xUnit, NUnit i MSTest default metody rozhraní podporují. Pokud testovací třída implementuje dvě testovací rozhraní, získává testy z obou — což je v .NET ekosystému obzvlášť užitečné díky striktní jedné base class.

### Python

Python nemá vestavěná rozhraní, ale protokolový polymorfismus a ABC (Abstract Base Classes) umožňují obdobný pattern. `unittest.TestCase` lze kombinovat s mixiny:

```python
class InterestRateCalculatorTestMixin:
    def create_calculator(self):
        raise NotImplementedError

    def test_rate_not_negative(self):
        calc = self.create_calculator()
        account = Account(AccountType.SAVINGS, 0)
        self.assertGreaterEqual(calc.calculate_rate(account), 0)

class StandardRateCalculatorTest(InterestRateCalculatorTestMixin, TestCase):
    def create_calculator(self):
        return StandardRateCalculator()
```

Pytest navíc podporuje *fixture parametrization* na úrovni tříd:

```python
@pytest.mark.parametrize("calc_cls", [StandardRateCalculator, PremiumRateCalculator])
class TestInterestRateCalculator:
    def test_rate_not_negative(self, calc_cls):
        calc = calc_cls()
        assert calc.calculate_rate(Account(AccountType.SAVINGS, 0)) >= 0
```

### Ruby (RSpec)

Ruby díky dynamické povaze a *shared examples* v RSpec dosahuje stejného cíle jinak — sdílené příklady se definují samostatně a vkládají do konkrétních testů:

```ruby
RSpec.shared_examples "interest rate calculator" do
  it "must not return negative rate" do
    rate = subject.calculate_rate(build(:account, :savings))
    expect(rate).to be >= 0
  end
end

RSpec.describe StandardRateCalculator do
  subject { described_class.new }
  it_behaves_like "interest rate calculator"
end
```

RSpec jde ještě dále — *shared context* umožňuje sdílet nejen testy, ale i setup (`let`, `before`).

## Kdy tento pattern použít a kdy ne

Pattern je vhodný, když:

- Existuje **více implementací téhož kontraktu** a všechny musí splňovat stejné invarianty.
- Sdílená logika se týká **čistě doménových pravidel**, nikoli infrastruktury.
- Chceme **vynutit**, aby každá nová implementace automaticky procházela existující sadou testů (smluvní testování — *contract testing*).

Naopak není vhodný, pokud:

- Sdílená testovací logika vyžaduje **složitou konfiguraci závislostí** — pak je vhodnější base class nebo test utility.
- Testy jsou převážně **integrační** s těžkou infrastrukturou.
- Rozhraní se mění často a každá změna v default testech by znamenala revizi napříč všemi implementacemi.

## Závěrem

Default metody rozhraní v JUnit 5 jsou praktický nástroj pro kompozitní testování. Umožňují definovat testovací kontrakty přímo vedle doménových rozhraní a vynucovat jejich splnění napříč implementacemi bez duplicity a bez křehké dědičnosti. Pattern není nový — obdobně ho řeší shared examples v Ruby, mixiny v Pythonu nebo extension funkce v Kotlinu — ale v Javě představuje nejčistší vyjádření myšlenky *smluvního testování*.

### Zdroje a další čtení

- [JUnit 5 User Guide — Writing Tests: Interfaces](https://junit.org/junit5/docs/current/user-guide/#writing-tests-interfaces)
- [Brian Goetz: Interface evolution in Java (YouTube, Oracle)](https://www.youtube.com/watch?v=Q1U4yN4Faw8)
- [Kotlin Language Docs — Interfaces](https://kotlinlang.org/docs/interfaces.html)
- [Microsoft Docs — Default interface methods (C#)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-8.0/default-interface-methods)
- [Pytest Documentation — Parametrization](https://docs.pytest.org/en/latest/how-to/parametrize.html)
- [RSpec Documentation — Shared examples](https://relishapp.com/rspec/rspec-core/docs/example-groups/shared-examples)
