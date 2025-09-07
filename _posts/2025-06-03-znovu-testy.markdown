---
layout: post
title: "Testy jako základ: Od TDD k udržitelným testům krok za krokem"
date:   2025-06-03 23:12:00 +0200
categories: [programování, testování, java, tdd]
tags: ["programming", "tests"]
---
## Úvod: Proč psát testy (a proč je psát dobře)?

V dnešním rychlém světě vývoje softwaru je kvalita klíčová. Jedním z nejlepších způsobů, jak ji zajistit, je **Test-Driven Development (TDD)** a psaní čistých, udržitelných testů.

## Výchozí bod: Implementace nové funkcionality (a první testy)

Představme si, že naším úkolem je implementovat API endpoint pro vytváření nového „Eligible Collateral“ pro danou protistranu.

### Produkční kód (EligibleCollateralSpringFacade.java)

```java
public class EligibleCollateralSpringFacade {
    public EligibleCollateralDetail create(final Counterparty counterparty, final EligibleCollateralCreate create) {
        validateCounterpartyId(counterparty);
        final var eligibleCollateral = create.toDomain(counterparty);
        final var stored = this.service.save(eligibleCollateral);
        return EligibleCollateralDetail.from(stored);
    }

    private void validateCounterpartyId(final Counterparty id) {
        // Validace existence protistrany
    }
}
```

### První testy (EligibleCollateralsAcceptanceTest.java)

```java
public class EligibleCollateralsAcceptanceTest {
    @Autowired MockMvc mockMvc;
    @Autowired AuthenticatedTestApi authenticationApi;

    @Test
    public void created_eligible_collaterals_contains_filled_in_information() throws Exception {
        authenticationApi.logInAsRisk();
        Long counterpartyId = 4L;
        String requestJson = "{"name": "Eligible Name", "isin": "isin123"}";

        mockMvc.perform(post("/api/non-clients/{id}/eligible-collaterals", counterpartyId)
                .content(requestJson)
                .contentType(MediaType.APPLICATION_JSON)
                .session(authenticationApi.getSession()))
            .andExpect(status().isOk())
            .andExpect(jsonPath("name").value("Eligible Name"))
            .andExpect(jsonPath("isin").value("isin123"));
    }
}
```

## Krok 1: Refaktoring – Vytvoření Test API

### EligibleCollateralsTestApi.java

```java
@Component
public class EligibleCollateralsTestApi {
    @Autowired MockMvc mockMvc;
    @Autowired AuthenticatedTestApi authenticationApi;

    public ResultActions create(Long counterpartyId, String eligibleCollateralJson) {
        return mockMvc.perform(post("/api/non-clients/{id}/eligible-collaterals", counterpartyId)
            .content(eligibleCollateralJson)
            .contentType(MediaType.APPLICATION_JSON)
            .session(authenticationApi.getSession()));
    }

    public AuthenticatedTestApi authentication() { return authenticationApi; }
    public Long existingNonClient() { return 4L; }
}
```

### Upravený test

```java
@Test
public void created_eligible_collaterals_contains_filled_in_information() throws Exception {
    api.authentication().logInAsRisk();
    Long counterpartyId = api.existingNonClient();
    String requestJson = "{"name": "Eligible Name", "isin": "isin123"}";

    api.create(counterpartyId, requestJson)
        .andExpect(status().isOk())
        .andExpect(jsonPath("name").value("Eligible Name"))
        .andExpect(jsonPath("isin").value("isin123"));
}
```

## Krok 2: Refaktoring – Zavedení Response Objectu

### EligibleCollateralsDetailReponse.java

```java
public class EligibleCollateralsDetailReponse {
    private final ResultActions resultActions;

    public EligibleCollateralsDetailReponse(ResultActions resultActions) {
        this.resultActions = resultActions;
    }

    public EligibleCollateralsDetailReponse expectStatusOk() {
        expect(status().isOk());
        return this;
    }

    public EligibleCollateralsDetailReponse expectName(String expected) {
        expect(jsonPath("name").value(expected));
        return this;
    }

    public void expectIsin(String expected) {
        expect(jsonPath("isin").value(expected));
    }

    private void expect(ResultMatcher matcher) {
        this.resultActions.andExpect(matcher);
    }
}
```

### Upravený test

```java
@Test
public void created_eligible_collaterals_contains_filled_in_information() {
    api.authentication().logInAsRisk();
    Long counterpartyId = api.existingNonClient();
    String requestJson = "{"name": "Eligible Name", "isin": "isin123"}";

    api.create(counterpartyId, requestJson)
        .expectStatusOk()
        .expectName("Eligible Name")
        .expectIsin("isin123");
}
```

## Krok 3: Refaktoring – Test Data Builder (EligibleDraft.java)

### EligibleDraft.java

```java
@Data
@AllArgsConstructor
@Accessors(fluent = true)
public class EligibleDraft {
    @Nullable @JsonProperty("name")
    private String name;

    @Nullable @JsonProperty("isin")
    private String isin;

    public String toJson() {
        final var om = new ObjectMapper();
        return om.writeValueAsString(this);
    }
}
```

### Rozšíření Test API

```java
public EligibleDraft prefilledEligibleDraft() {
    return new EligibleDraft("Default Eligible Name", "DefaultISIN123");
}

public EligibleCollateralsDetailReponse create(Long counterparty, EligibleDraft draft) {
    return create(counterparty, draft.toJson());
}
```

### Test s builderem

```java
@Test
public void created_eligible_collaterals_contains_filled_in_information() {
    api.authentication().logInAsRisk();
    final var counterparty = api.existingNonClient();
    final var draft = api.prefilledEligibleDraft()
                         .name("Eligible Name")
                         .isin("isin123");

    api.create(counterparty, draft)
        .expectStatusOk()
        .expectName("Eligible Name")
        .expectIsin("isin123");
}
```

## TDD v praxi: Přidání kontroly oprávnění

### Test na nedostatečná oprávnění

```java
@Test
public void create_eligible_with_insufficient_permissions_collaterals_returns_error() {
    api.authentication().logInAsBranchWorker();
    final var counterparty = api.existingNonClient();
    final var draft = api.prefilledEligibleDraft();

    api.create(counterparty, draft)
        .expectStatusForbidden();
}
```

### Implementace zabezpečení

```java
@Secured(DafosPermissions.EDIT_COUNTERPARTY_LIMITS)
public EligibleCollateralDetail create(final Counterparty counterparty, final EligibleCollateralCreate create) {
    validateCounterpartyId(counterparty);
    final var eligibleCollateral = create.toDomain(counterparty);
    final var stored = this.service.save(eligibleCollateral);
    return EligibleCollateralDetail.from(stored);
}
```

## Závěr: Investice, která se vyplatí

Dobře navržená testovací infrastruktura přináší:

- Větší důvěru při refaktoringu.
- Rychlejší odhalování chyb.
- Lepší dokumentaci systému.
- Snazší onboarding nových vývojářů.
- Nižší náklady na údržbu a rozvoj aplikace.
