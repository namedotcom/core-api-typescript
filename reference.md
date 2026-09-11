# Reference
<details><summary><code>client.<a href="/src/Client.ts">hello</a>() -> Namecom.HelloResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns basic information about the API server (useful for testing connectivity and version checks).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.hello();

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**requestOptions:** `NamecomClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Account Info
<details><summary><code>client.accountInfo.<a href="/src/api/resources/accountInfo/client/Client.ts">checkAccountBalance</a>() -> Namecom.CheckAccountBalanceResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns the current account credit balance for the authenticated user.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.accountInfo.checkAccountBalance();

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**requestOptions:** `AccountInfoClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Accounts
<details><summary><code>client.accounts.<a href="/src/api/resources/accounts/client/Client.ts">createAccount</a>({ ...params }) -> Namecom.CreateAccountResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Creates a new sub-account under your authenticated reseller account and returns API credentials for the new account.  This endpoint is only available to approved reseller accounts. Contact name.com support to request access.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.accounts.createAccount({
    account: {
        accountName: "reseller_subaccount",
        contacts: {
            registrant: {
                firstName: "Jane",
                lastName: "Doe",
                address1: "123 Main St.",
                city: "Denver",
                state: "CO",
                zip: "12345",
                country: "US",
                email: "admin@example.net",
                phone: "+13035551212"
            }
        },
        password: "SecureP4ss!"
    },
    apiTos: true,
    tos: true
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CreateAccountRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `AccountsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Domains
<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">listDomains</a>({ ...params }) -> Namecom.ListDomainsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Lists all domains in your account (basic details for each domain).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.listDomains();

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListDomainsRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">createDomain</a>({ ...params }) -> Namecom.CreateDomainResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Registers a new domain under your account. You must provide `domain.domainName` at minimum.
This endpoint is commonly used to programmatically onboard new domains through user signup flows or checkout experiences.

If no contacts are passed in this request, the default contacts for your name.com account will be used.

### Create Domain pricing

See the [Domain purchase pricing guide](/guides/domain-pricing) for the full reference.
**Recommendation:** For most integrations, scope discovery to `purchaseType: registration`. Other purchase types are supported but add complexity — details in the guide above.

**Discovery (required before create):** Call [Search](/api/v1/reference/domains/search) or [Check Availability](/api/v1/reference/domains/check-availability), not Get Pricing alone. Both return the same `SearchResult` fields (`purchaseType`, `purchasePrice`, `premium`, `purchasable`). [Zone Check](/api/v1/reference/domains/zone-check) is designed for rapid availability checks only; it is not sufficient to complete a purchase.

### Getting the price for Create Domain

1. **Search or Check Availability** → copy `purchaseType`, `premium`, note `purchasePrice`.

2. Branch on `purchaseType`:
   - **`registration` + `premium: false`** — omit `purchasePrice` on create, set `years`. Optional: Get Pricing with same `years` to preview the total.
   - **`registration` + `premium: true`** — Get Pricing with same `years` → pass `purchasePrice` exactly.
   - **aftermarket / expiring / backorder** — use discovery `purchasePrice` (flat fee). Re-check discovery before create. Do not use Get Pricing for create price. `years` does not multiply price or guarantee registration length.

3. If `purchasePrice` is sent, it must match exactly or the request fails with `400` and `"Purchase price does not match"`.

**Years on acquisition types:** For `aftermarket_s`, `aftermarket_b`, `aftermarket_i`, `expiring`, and `backorder`: omit `years` or pass the TLD default. Check `domain.expireDate` in the response; [Renew](/api/v1/reference/domains/renew-domain) to extend registration.

### Best Practices For Domain Creates

In general, you should check that a domain is available prior to attempting to purchase a domain.
You can use either the [checkAvailability](/api/v1/reference/domains/check-availability) endpoint, or the [Search](/api/v1/reference/domains/search) endpoint
to confirm that a domain is purchasable.

#### Important Note on Dropcatching and Abuse Prevention

_The createDomain endpoint is designed for standard domain registrations and is not intended for automated dropcatching (i.e., mass or high-frequency attempts to register domains the moment they become available after expiration). The use of drop-catching tools or services to acquire expired domains is strictly prohibited. All domain acquisitions must go through approved channels to ensure fair and transparent access._

#### Contact Verification
When a new domain registration is created and a contact is submitted, name.com may need to validate the contact's email address in accordance with ICANN policy. This validation involves sending an email to the provided address, prompting the recipient to click a link to verify their email address.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.createDomain({
    domain: {
        domainName: "example.com"
    }
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CreateDomainRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.IdempotentRequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">getDomain</a>({ ...params }) -> Namecom.DomainResponsePayload</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves detailed information for a specific domain in your account.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.getDomain({
    domainName: "example.com"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetDomainRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">updateDomain</a>({ ...params }) -> Namecom.DomainResponsePayload</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Allows updating of the autorenew, WhoIs Privacy and lock status of the specified domain. The request requires one, or any combination of the parameters in order to pass validation. If any of the requested updates failed, the domain will be returned to it's original state.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.updateDomain({
    domainName: "domainName"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.UpdateDomainRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">disableAutorenew</a>({ ...params }) -> Namecom.Domain</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Turns off automatic renewal for a domain. **DEPRECATED** This endpoint is deprecated in favor of the new UpdateDomain API. This will be removed in a future release.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.disableAutorenew({
    domainName: "example.com",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DisableAutorenewRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">disableWhoisPrivacy</a>({ ...params }) -> Namecom.Domain</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Disables WHOIS privacy protection on a domain. **DEPRECATED** This endpoint is deprecated in favor of the new UpdateDomain API. This will be removed in a future release.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.disableWhoisPrivacy({
    domainName: "example.com",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DisableWhoisPrivacyRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">enableAutorenew</a>({ ...params }) -> Namecom.Domain</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Turns on automatic renewal for a domain. **DEPRECATED** This endpoint is deprecated in favor of the new UpdateDomain API. This will be removed in a future release.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.enableAutorenew({
    domainName: "example.com",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.EnableAutorenewRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">enableWhoisPrivacy</a>({ ...params }) -> Namecom.Domain</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Enables WHOIS privacy protection on a domain. **DEPRECATED** This endpoint is deprecated in favor of the new UpdateDomain API. This will be removed in a future release.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.enableWhoisPrivacy({
    domainName: "domainName",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.EnableWhoisPrivacyRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">getAuthCodeForDomain</a>({ ...params }) -> Namecom.AuthCodeResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves the transfer authorization code (EPP code) for a domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.getAuthCodeForDomain({
    domainName: "domainName"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetAuthCodeForDomainRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">getPricingForDomain</a>({ ...params }) -> Namecom.PricingResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns registration, renewal, and transfer pricing for a domain and term.

**Not a discovery endpoint:** Does not return `purchaseType`. Cannot determine whether a domain is acquired via registration vs aftermarket/expiring/backorder — call [Search](/api/v1/reference/domains/search) or [Check Availability](/api/v1/reference/domains/check-availability) first.

**Scope:** `purchasePrice` and `premium` reflect **standard and registry-premium registration** only. They do **not** return aftermarket, expiring, or backorder acquisition prices. For those types, use `purchasePrice` from Search or Check Availability.

**Registration create (`purchaseType: registration`):** When create requires `purchasePrice` (registry premium), call with the **same** `years` you will send on create. Pass `purchasePrice` directly — it is the **total** for that term, not a per-year component.

**Renew:** Pass `renewalPrice` as `purchasePrice` on [Renew Domain](/api/v1/reference/domains/renew-domain) for premium renewals — not for computing Create Domain totals.

**Transfer:** Pass `transferPrice` as `purchasePrice` on [Create Transfer](/api/v1/reference/transfers/create-transfer) for premium transfers. The `years` query parameter does not affect `transferPrice`.

See the [Domain pricing guide](/guides/domain-pricing) for the full workflow.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.getPricingForDomain({
    domainName: "domainName",
    years: 2
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetPricingForDomainRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">lockDomain</a>({ ...params }) -> Namecom.Domain</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Locks a domain to prevent it from being transferred. **DEPRECATED** This endpoint is deprecated in favor of the new UpdateDomain API. This will be removed in a future release.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.lockDomain({
    domainName: "example.com",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.LockDomainRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">purchasePrivacy</a>({ ...params }) -> Namecom.PrivacyResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Adds or renews WHOIS privacy protection for a domain. This is used to ensure personal contact details remain hidden from public WHOIS lookups.  If WHOIS privacy is already enabled, this will extend the protection. If it’s not yet active, this will enable the service.  WHOIS privacy is free for API users and does not add a fee.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.purchasePrivacy({
    domainName: "domainName"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DomainsPurchasePrivacyBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.IdempotentRequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">renewDomain</a>({ ...params }) -> Namecom.RenewDomainResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Renews an existing domain for an additional registration period. Include the domain name and renewal term. Omit `purchasePrice` for standard (non-premium) renewals. For premium renewals, pass `renewalPrice` from [Get Pricing](/api/v1/reference/domains/get-pricing-for-domain) with matching `years` as `purchasePrice`. Renewal pricing is separate from Create Domain registration/acquisition pricing. This is typically used to extend ownership before a domain’s expiration.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.renewDomain({
    domainName: "domainName"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DomainsRenewDomainBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">setContacts</a>({ ...params }) -> Namecom.DomainResponsePayload</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Updates WHOIS contact information for a domain. This includes the registrant, administrative, technical, and billing contacts.  All contact objects must be complete — partial updates are not supported.  You should fetch the existing contact data first (e.g., via [GetDomain](/api/v1/reference/domains/get-domain) and modify only the values you wish to change.  This call replaces all four contact sets at once.
#### Contact Verification
When registrant contact information is updated, validation may be triggered if the new contact information has not been previously validated. This validation is required by ICANN for all TLDs except country-code TLDs (ccTLDs). This validation involves sending an email to the provided address, prompting the recipient to click a link to verify their email address.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.setContacts({
    domainName: "example.com",
    contacts: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DomainsSetContactsBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">setNameservers</a>({ ...params }) -> Namecom.DomainResponsePayload</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

SetNameservers will set the nameservers for the Domain. This operation updates the DNS configuration by changing which nameservers are responsible for the domain's zone.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.setNameservers({
    domainName: "example.com",
    nameservers: ["ns1.name.com", "ns2.name.com"]
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DomainsSetNameserversBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">unlockDomain</a>({ ...params }) -> Namecom.Domain</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Unlocks a domain to allow it to be transferred. **DEPRECATED** This endpoint is deprecated in favor of the new UpdateDomain API. This will be removed in a future release.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.unlockDomain({
    domainName: "domainName",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.UnlockDomainRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">checkAvailability</a>({ ...params }) -> Namecom.SearchResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Checks whether up to 50 domain names are purchasable and returns **discovery** pricing for each result.

**Discovery endpoint:** Returns `SearchResult` fields — `purchaseType`, `purchasePrice`, `premium`, `purchasable`. [Search](/api/v1/reference/domains/search) returns the same fields for keyword/suggestion flows. Use this endpoint to determine what to send on [Create Domain](/api/v1/reference/domains/create-domain).

When results show `premium: true` or a non-`registration` `purchaseType`, follow the [Domain pricing guide](/guides/domain-pricing) before calling Create Domain. For non-registration types, re-check Check Availability immediately before create — acquisition prices can change.

**Recommendation:** Set `purchaseType` to `registration`. Most resellers 
restrict results to domains with a `purchaseType` of `registration`
to ensure predictable pricing and immediate fulfillment. Other purchase types
(such as aftermarket variants) can introduce higher costs and non-instant
transactions that may be delayed or declined by third parties.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.checkAvailability({
    domainNames: ["domainNames"]
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.AvailabilityRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">search</a>({ ...params }) -> Namecom.SearchResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Searches for domain name suggestions based on a keyword or term. Important: Do not
encode the `:` in the path. Use `/core/v1/domains:search`, not `/core/v1/domains%3Asearch`.

**Discovery endpoint:** Returns `SearchResult` fields — `purchaseType`, `purchasePrice`, `premium`, `purchasable`.

**Recommendation:** Set `purchaseType` to `registration`. Most resellers restrict
results to domains with a `purchaseType` of `registration` to ensure predictable
pricing and immediate fulfillment. Other purchase types (such as aftermarket) can
introduce higher costs and non-instant transactions that may be delayed or declined
by third parties.
With `purchaseType: registration`, domains that do not match the filter are **omitted** from results (unlike Check Availability, which returns them with `purchasable: false`).

When results show `premium: true` or a non-`registration` `purchaseType`, follow the [Domain pricing guide](/guides/domain-pricing) before calling Create Domain. For all types, re-check with Check Availability immediately before create — prices and availability can change.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.search({
    keyword: "mydomain"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.SearchRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domains.<a href="/src/api/resources/domains/client/Client.ts">zoneCheck</a>({ ...params }) -> Namecom.ZoneCheckResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Zone Check offers a rapid, preliminary check for domain availability by leveraging cached zone file data.  Ideal for large-batch queries, it provides a high confidence indication of a domain's availability significantly faster than live registry checks.  For definitive, real-time availability and pricing, you can follow up with the standard [Check Availability](/api/v1/reference/domains/check-availability) call.
The API normalizes and validates each submitted domain string. Domains that fail validation, use an unsupported TLD for this service, or  are otherwise not eligible for zone check are **removed** from the request before the zone file lookup runs. The response includes **only**  a numeric count of removed domains (`removed`); individual removed strings are not returned. A future API version may extend the contract to  include details about removed domains.

For the best results and to avoid `400 Bad Request` errors after cleaning, ensure each domain string meets the criteria described for  `domainNames` in the request body schema.

If no valid domains remain after this process, the API returns a `400 Bad Request` response.
**Note:** The cached zone files used for this check are refreshed twice daily based on the latest available data from the registries.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domains.zoneCheck({
    domainNames: ["example.com", "example.net", "example.org"]
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ZoneCheckRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## DNSSECs
<details><summary><code>client.dnsseCs.<a href="/src/api/resources/dnsseCs/client/Client.ts">listDnsseCs</a>({ ...params }) -> Namecom.ListDnsseCsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Lists all DNSSEC (DS) records configured for a domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dnsseCs.listDnsseCs({
    domainName: "domainName"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListDnsseCsRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsseCsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.dnsseCs.<a href="/src/api/resources/dnsseCs/client/Client.ts">createDnssec</a>({ ...params }) -> Namecom.Dnssec</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Adds (registers) a new DNSSEC DS record for a domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dnsseCs.createDnssec({
    domainName: "domainName",
    algorithm: 1,
    digest: "digest",
    digestType: 1,
    keyTag: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CreateDnssecBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsseCsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.dnsseCs.<a href="/src/api/resources/dnsseCs/client/Client.ts">getDnssec</a>({ ...params }) -> Namecom.Dnssec</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves details of a specific DNSSEC record for a domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dnsseCs.getDnssec({
    domainName: "domainName",
    digest: "digest"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetDnssecRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsseCsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.dnsseCs.<a href="/src/api/resources/dnsseCs/client/Client.ts">deleteDnssec</a>({ ...params }) -> void</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Deletes a DNSSEC record from a domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dnsseCs.deleteDnssec({
    domainName: "domainName",
    digest: "digest"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DeleteDnssecRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsseCsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Email Forwardings
<details><summary><code>client.emailForwardings.<a href="/src/api/resources/emailForwardings/client/Client.ts">listEmailForwardings</a>({ ...params }) -> Namecom.ListEmailForwardingsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns a paginated list of all email forwarding rules for a domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.emailForwardings.listEmailForwardings({
    domainName: "domainName",
    perPage: 100,
    page: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListEmailForwardingsRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `EmailForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.emailForwardings.<a href="/src/api/resources/emailForwardings/client/Client.ts">createEmailForwarding</a>({ ...params }) -> Namecom.EmailForwarding</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Creates a new email forwarding rule for a domain, such as redirecting info@example.com to an external inbox.  If this is the first email forwarding rule created for the domain, the API may also update your MX records automatically to enable mail routing.  The alias must not conflict with existing email services or MX records.  Wildcard and catch-all forwarding is not supported, so an `emailBox` containing `*` is rejected with a `400 Bad Request`.  To modify a forwarding rule later, use [UpdateEmailForwarding](/api/v1/reference/email-forwardings/update-email-forwarding).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.emailForwardings.createEmailForwarding({
    domainName: "example.com",
    emailBox: "admin",
    emailTo: "webmaster@example.com"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CreateEmailForwardingRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `EmailForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.emailForwardings.<a href="/src/api/resources/emailForwardings/client/Client.ts">getEmailForwarding</a>({ ...params }) -> Namecom.EmailForwarding</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves the details of a specific email forwarding entry.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.emailForwardings.getEmailForwarding({
    domainName: "domainName",
    emailBox: "emailBox"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetEmailForwardingRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `EmailForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.emailForwardings.<a href="/src/api/resources/emailForwardings/client/Client.ts">updateEmailForwarding</a>({ ...params }) -> Namecom.EmailForwarding</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Updates the destination email address for an existing forwarding rule.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.emailForwardings.updateEmailForwarding({
    domainName: "domainName",
    emailBox: "emailBox",
    emailTo: "emailTo"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.EmailForwardingsUpdateEmailForwardingBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `EmailForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.emailForwardings.<a href="/src/api/resources/emailForwardings/client/Client.ts">deleteEmailForwarding</a>({ ...params }) -> void</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Deletes an email forwarding rule from a domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.emailForwardings.deleteEmailForwarding({
    domainName: "domainName",
    emailBox: "emailBox"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DeleteEmailForwardingRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `EmailForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## DNS
<details><summary><code>client.dns.<a href="/src/api/resources/dns/client/Client.ts">listRecords</a>({ ...params }) -> Namecom.ListRecordsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Lists all DNS records for a specified domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dns.listRecords({
    domainName: "domainName"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListRecordsRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.dns.<a href="/src/api/resources/dns/client/Client.ts">createRecord</a>({ ...params }) -> Namecom.Record_</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Adds a new DNS record to the specified domain zone. Provide the record type (e.g. A, MX, CNAME), host, value, and TTL.  This is used for configuring domain-based services such as email, website hosting, or third-party verifications.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dns.createRecord({
    domainName: "domainName",
    answer: "answer",
    host: "host",
    type: "A"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DnsCreateRecordBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.dns.<a href="/src/api/resources/dns/client/Client.ts">getRecord</a>({ ...params }) -> Namecom.Record_</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves details of a specific DNS record.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dns.getRecord({
    domainName: "domainName",
    id: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetRecordRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.dns.<a href="/src/api/resources/dns/client/Client.ts">updateRecord</a>({ ...params }) -> Namecom.Record_</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Replaces an existing DNS record with new data. This is a full overwrite — all required fields (host, type, answer, ttl) must be included in the request body. If you omit a field, the existing value will not be preserved and the request may fail. Use [GetRecord](/api/v1/reference/dns/get-record) beforehand to retrieve the current values if you intend to modify just one field. The record ID must belong to a domain you manage.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dns.updateRecord({
    domainName: "domainName",
    id: 1,
    answer: "answer",
    type: "A"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DnsUpdateRecordBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.dns.<a href="/src/api/resources/dns/client/Client.ts">deleteRecord</a>({ ...params }) -> void</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Removes a DNS record by ID. Often used during cleanup operations or when replacing outdated DNS settings with updated records.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.dns.deleteRecord({
    domainName: "domainName",
    id: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DeleteRecordRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DnsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## URL Forwardings
<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">listUrlForwardings</a>({ ...params }) -> Namecom.ListUrlForwardingsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns all URL forwarding settings configured for a domain. **Deprecated.** Use [List URL Forwardings by domain](/api/v1/reference/url-forwardings/list-urlforwardings-by-domain) instead, which returns entries with an `id` for use with by-ID endpoints.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.listUrlForwardings({
    domainName: "example.com",
    perPage: 100,
    page: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListUrlForwardingsRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">createUrlForwarding</a>({ ...params }) -> Namecom.UrlForwardingResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Sets up a new URL forwarding (redirect) for a domain or subdomain. If this is the first URL forwarding entry, it may modify the A records for the domain accordingly. Note that changes may take up to 24 hours to fully propagate.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.createUrlForwarding({
    domainName: "example.com",
    forwardsTo: "https://destination-site.com",
    host: "www",
    type: "masked"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.UrlForwardingInput` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">getUrlForwarding</a>({ ...params }) -> Namecom.UrlForwardingResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves the details of a specific URL forwarding configuration. **Deprecated.** Use [Get URL Forwarding by ID](/api/v1/reference/url-forwardings/get-urlforwarding-by-id) instead.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.getUrlForwarding({
    domainName: "example.com",
    host: "www.example.org"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetUrlForwardingRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">updateUrlForwarding</a>({ ...params }) -> Namecom.UrlForwardingResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Modifies an existing URL forwarding rule. Changes may take up to 24 hours to fully propagate. **Deprecated.** Use [Update URL Forwarding by ID](/api/v1/reference/url-forwardings/update-urlforwarding-by-id) instead.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.updateUrlForwarding({
    domainName: "example.com",
    host: "www.example.org",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.UpdateUrlForwardingRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">deleteUrlForwarding</a>({ ...params }) -> void</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Removes a URL forwarding configuration from the domain. This operation cannot be undone. **Deprecated.** Use [Delete URL Forwarding by ID](/api/v1/reference/url-forwardings/delete-urlforwarding-by-id) instead.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.deleteUrlForwarding({
    domainName: "example.com",
    host: "www.example.org"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DeleteUrlForwardingRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">listUrlForwardingsByDomain</a>({ ...params }) -> Namecom.ListUrlForwardingsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns all URL forwarding settings configured for a domain. Each entry includes an `id` that can be used with the URL Forwarding by-ID endpoints to get, update, or delete records.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.listUrlForwardingsByDomain({
    domainName: "example.com",
    perPage: 100,
    page: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListUrlForwardingsByDomainRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">getUrlForwardingById</a>({ ...params }) -> Namecom.UrlForwardingResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves the details of a specific URL forwarding configuration by ID.  The domain must be owned by the authenticated account.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.getUrlForwardingById({
    domainName: "example.com",
    id: 12345
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetUrlForwardingByIdRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">deleteUrlForwardingById</a>({ ...params }) -> void</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Removes a URL forwarding configuration by ID. The domain must be owned by the authenticated account. This operation cannot be undone.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.deleteUrlForwardingById({
    domainName: "example.com",
    id: 12345
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DeleteUrlForwardingByIdRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.urlForwardings.<a href="/src/api/resources/urlForwardings/client/Client.ts">updateUrlForwardingById</a>({ ...params }) -> Namecom.UrlForwardingResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Modifies an existing URL forwarding rule by ID.  The domain must be owned by the authenticated account. Changes may take up to 24 hours to fully propagate.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.urlForwardings.updateUrlForwardingById({
    domainName: "example.com",
    id: 12345,
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.UpdateUrlForwardingByIdRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `UrlForwardingsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Vanity Nameservers
<details><summary><code>client.vanityNameservers.<a href="/src/api/resources/vanityNameservers/client/Client.ts">listVanityNameservers</a>({ ...params }) -> Namecom.ListVanityNameserversResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Lists all vanity nameserver hostnames configured for a domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.vanityNameservers.listVanityNameservers({
    domainName: "example.com",
    perPage: 50,
    page: 2
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListVanityNameserversRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `VanityNameserversClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.vanityNameservers.<a href="/src/api/resources/vanityNameservers/client/Client.ts">createVanityNameserver</a>({ ...params }) -> Namecom.VanityNameserverResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Register a new vanity nameserver for the specified domain.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.vanityNameservers.createVanityNameserver({
    domainName: "example.com",
    hostname: "ns1",
    ips: ["192.168.1.10", "2001:0db8:85a3:0000:0000:8a2e:0370:7334"]
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CreateVanityNameserverBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `VanityNameserversClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.vanityNameservers.<a href="/src/api/resources/vanityNameservers/client/Client.ts">getVanityNameserver</a>({ ...params }) -> Namecom.VanityNameserverResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves details for a of a specific vanity nameserver (including its IP addresses).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.vanityNameservers.getVanityNameserver({
    domainName: "example.com",
    hostname: "ns1.example.com"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetVanityNameserverRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `VanityNameserversClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.vanityNameservers.<a href="/src/api/resources/vanityNameservers/client/Client.ts">updateVanityNameserver</a>({ ...params }) -> Namecom.VanityNameserverResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Updates the glue record IP addresses for a vanity nameserver.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.vanityNameservers.updateVanityNameserver({
    domainName: "example.com",
    hostname: "ns1.example.com",
    ips: ["192.168.1.10", "2001:0db8:85a3:0000:0000:8a2e:0370:7334"]
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.UpdateVanityNameserverBody` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `VanityNameserversClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.vanityNameservers.<a href="/src/api/resources/vanityNameservers/client/Client.ts">deleteVanityNameserver</a>({ ...params }) -> void</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Deletes a vanity nameserver from the domain’s registry settings. This operation might fail if the registry detects the nameserver is still in use.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.vanityNameservers.deleteVanityNameserver({
    domainName: "example.com",
    hostname: "ns1.example.com"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DeleteVanityNameserverRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `VanityNameserversClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Webhook Notifications
<details><summary><code>client.webhookNotifications.<a href="/src/api/resources/webhookNotifications/client/Client.ts">getSubscribedNotifications</a>() -> Namecom.ListSubscribedWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves all active webhook subscriptions on the account.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.webhookNotifications.getSubscribedNotifications();

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**requestOptions:** `WebhookNotificationsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.webhookNotifications.<a href="/src/api/resources/webhookNotifications/client/Client.ts">subscribeToNotification</a>({ ...params }) -> Namecom.SubscribeToNotificationResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Creates a webhook subscription to receive real-time notifications about specific domain or account events (e.g. transfer completions, renewals). Pass the callback URL and event types. This allows external systems to stay in sync with name.com changes.
Supported webhook event names:
- `account.credit.balance_change` – account credit balance changes (increases or decreases).
- `account.domain.removal` – domain removed from the subscribing account.
- `domain.lock.status_change` – domain lock added or removed.
- `domain.transfer.status_change` – domain transfer IN to name.com; status updates while name.com is the gaining registrar.
- `domain.transfer_out.status_change` – domain transfer OUT from name.com; `initiated`, `completed` (domain removed), or `canceled` (no longer pending at the registry).
- `domain.transfer.internal_in` - name.com domain transfers in to the subscribing account via internal transfer.
- `domain.transfer.internal_out` - name.com domain transfers out of the subscribing account via internal transfer.
- `contact.verification.status_change` - contact verification status changes (verified or unverified).
- `domain.registry.rejection` – domain **create** failed after asynchronous registry processing (uncommon; most creates succeed at request time).
- `domain.expiration` – domain has expired and entered the post-expiry grace period. This is informational only.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.webhookNotifications.subscribeToNotification({
    eventName: "account.credit.balance_change",
    url: "https://example.com",
    active: true
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.SubscribeToNotification` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `WebhookNotificationsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.webhookNotifications.<a href="/src/api/resources/webhookNotifications/client/Client.ts">modifySubscription</a>({ ...params }) -> Namecom.ModifySubscriptionResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Updates an existing webhook’s configuration.  This may include changing the callback URL or updating whether the webhook is currently active.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.webhookNotifications.modifySubscription({
    id: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ModifySubscriptionRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `WebhookNotificationsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.webhookNotifications.<a href="/src/api/resources/webhookNotifications/client/Client.ts">deleteSubscription</a>({ ...params }) -> void</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Removes a webhook subscription from the account.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.webhookNotifications.deleteSubscription({
    id: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DeleteSubscriptionRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `WebhookNotificationsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Orders
<details><summary><code>client.orders.<a href="/src/api/resources/orders/client/Client.ts">listOrders</a>({ ...params }) -> Namecom.ListOrdersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves a list of all orders placed in the account.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.orders.listOrders();

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListOrdersRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `OrdersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.orders.<a href="/src/api/resources/orders/client/Client.ts">getOrder</a>({ ...params }) -> Namecom.Order</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Fetches full details about a specific order using its ID. This includes domains, prices, and timestamps.  Useful for confirming transactions, receipts, or generating invoices.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.orders.getOrder({
    orderId: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetOrderRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `OrdersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Refunds
<details><summary><code>client.refunds.<a href="/src/api/resources/refunds/client/Client.ts">processRefund</a>({ ...params }) -> Namecom.RefundResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Deletes eligible domains and security products during the Add Grace Period (AGP) and automatically issues refunds for the associated order items.

### Eligibility Requirements

- **Product Types**: Only `registration` and `whois_privacy` product types are eligible for refunds.
- **AGP Timing**: Items must be within the Add Grace Period (typically 5 days from registration, varies by TLD).
- **Order Ownership**: All `orderItemIds` must belong to the specified `orderId`.

### Refund Processing

Refunds are processed in the following order:
1. Domain deletion is attempted for each eligible order item
2. Upon successful deletion, the refund is issued
3. Refunds are sent to the original payment method on file
4. If the original payment method is unavailable, the refund is credited to the account balance

### Idempotency

This endpoint supports idempotent requests via the `X-Idempotency-Key` header. If you retry a request with the same idempotency key, you will receive the same response as the original request. This is useful for safely retrying requests without risk of processing duplicate refunds.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.refunds.processRefund({
    orderId: 123456,
    orderItemIds: [987654]
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.RefundRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `RefundsClient.IdempotentRequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Transfers
<details><summary><code>client.transfers.<a href="/src/api/resources/transfers/client/Client.ts">listTransfers</a>({ ...params }) -> Namecom.ListTransfersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns all domain transfer requests for the account, including in-progress and recent transfers.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.transfers.listTransfers();

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ListTransfersRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `TransfersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.transfers.<a href="/src/api/resources/transfers/client/Client.ts">createTransfer</a>({ ...params }) -> Namecom.CreateTransferResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Initiates a domain transfer into your name.com account from another registrar. You must provide the domain name and its valid transfer authorization code (EPP code). The domain must not be locked or under any transfer restrictions (e.g. clientTransferProhibited). If successful, the transfer is submitted and tracked through the ICANN transfer process. Once a transfer has been created, you can track its progress via the [GetTransfer](/api/v1/reference/transfers/get-transfer) endpoint.
**Transfer pricing:** Omit `purchasePrice` for standard (non-premium) transfers. For premium transfers, pass `transferPrice` from [Get Pricing For Domain](/api/v1/reference/domains/get-pricing-for-domain) as `purchasePrice`. If sent, it must match Get Pricing `transferPrice` exactly or the request will fail. Premium transfers without `purchasePrice` will fail. See the [Domain pricing guide](/guides/domain-pricing) for how [Get Pricing](/api/v1/reference/domains/get-pricing-for-domain) `transferPrice` relates to the `years` query parameter.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.transfers.createTransfer({
    authCode: "ABC123",
    domainName: "example.com"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CreateTransferRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `TransfersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.transfers.<a href="/src/api/resources/transfers/client/Client.ts">getTransfer</a>({ ...params }) -> Namecom.Transfer</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retrieves details of a specific domain transfer request.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.transfers.getTransfer({
    domainName: "domainName"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetTransferRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `TransfersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.transfers.<a href="/src/api/resources/transfers/client/Client.ts">cancelTransfer</a>({ ...params }) -> Namecom.Transfer</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Cancels a pending transfer request. This can be used if the transfer was initiated in error or if the authorization code provided was incorrect.
The price of the transfer will refund the amount to account credit.

Cancelable statuses:
- pending
- submitting_transfer
- pending_new_auth_code
- pending_unlock
- pending_registry_unlock
- rejected

Non-cancelable statuses:
- pending_transfer
- pending_insert
- completed
- failed
- canceled
- canceled_pending_refund
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.transfers.cancelTransfer({
    domainName: "domainName",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CancelTransferRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `TransfersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.transfers.<a href="/src/api/resources/transfers/client/Client.ts">cancelOutboundTransfer</a>({ ...params }) -> Namecom.CancelTransferOutResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Cancels an outbound transfer for the given domain. Use this when the domain is being transferred out of name.com (losing registrar) to another (gaining) registrar and the registrant or reseller wants to cancel that transfer.
On success, subscribers receive `domain.transfer_out.status_change` with status `canceled`.
The endpoint validates that the domain exists and belongs to the authenticated account. Only domains in a pending transfer (out) state can be canceled.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.transfers.cancelOutboundTransfer({
    domainName: "example.com",
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CancelOutboundTransferRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `TransfersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.transfers.<a href="/src/api/resources/transfers/client/Client.ts">createInternalTransferIn</a>({ ...params }) -> Namecom.DomainResponsePayload</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Pulls a domain from another [name.com](https://www.name.com) account into your reseller (gaining) account using a valid authorization code. This is an **internal** name.com-to-name.com move; it is separate from [Create Transfer](/api/v1/reference/transfers/create-transfer), which brings domains in from **external** registrars.
Check if a TLD is eligible for internal transfer in by calling [Tld Requirements](/api/v1/reference/domaininfo/requirementsV2) for the TLD and checking property `supportsInternalTransfer`.
This API is only available to approved reseller accounts. Contact name.com support to request access.
#### Losing account (dashboard only)
The party that holds the domain today must use the name.com dashboard on the **losing** account to **unlock** the domain (remove registrar transfer lock) and to **copy the authorization code** to provide to your integration. This endpoint does not unlock the domain or retrieve the auth code for the losing account.
#### Gaining account (this API)
Call this endpoint with `domainName`, `authCode`, and optional `contacts` using the **gaining** reseller's API credentials.
#### Contacts and post-transfer lock
If `contacts` is omitted, the gaining account's default contacts are applied. If `contacts` is provided, any roles included in the request are applied and omitted roles use the gaining account's default contacts (same pattern as [Create Domain](/api/v1/reference/domains/create-domain) and [Set Contacts](/api/v1/reference/domains/set-contacts)). The 60-day contact-change transfer lock is enforced based on the **gaining** account's settings, consistent with Set Contacts.
#### Access
Restricted to approved enterprise resellers; other callers receive `403 Forbidden`.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.transfers.createInternalTransferIn({
    domainName: "example.com",
    authCode: "ABC123"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.CreateInternalTransferInRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `TransfersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.transfers.<a href="/src/api/resources/transfers/client/Client.ts">getTransferEligibility</a>({ ...params }) -> Namecom.TransferEligibilityResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns whether a domain is currently registered at [name.com](https://www.name.com) and whether the TLD supports internal transfer between name.com accounts. Use this to decide whether to send your user through the [Create Transfer](/api/v1/reference/transfers/create-transfer) external transfer flow or the [Create Internal Transfer In](/api/v1/reference/transfers/create-internal-transfer-in) flow before initiating a transfer-in.

#### Response semantics

`atName` is `true` if the domain is currently registered at name.com in any account. This information is also publicly available via RDAP.

`supportsInternalTransfer` mirrors the TLD-level value returned by [Tld Requirements](/api/v1/reference/domain-info/get-specific-tld-requirements). It indicates whether the TLD is eligible for internal transfer between name.com accounts. It does not reflect per-account allowlist eligibility — if your account is not allowlisted for internal transfer in, calling [Create Internal Transfer In](/api/v1/reference/transfers/create-internal-transfer-in) will return `403 Forbidden`.

#### Privacy

This endpoint never reveals which account a domain is in. To check whether a domain is in your own account, use [Get Domain](/api/v1/reference/domains/get-domain) instead.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.transfers.getTransferEligibility({
    domainName: "domainName"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetTransferEligibilityRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `TransfersClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Domain Info
<details><summary><code>client.domainInfo.<a href="/src/api/resources/domainInfo/client/Client.ts">getRequirement</a>({ ...params }) -> Namecom.GetRequirementResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns the registration requirements some general information for a specific TLD. The response contains a detailed description of eligibility criteria and a fields object with all required and optional fields, including validation rules, conditional logic, and nested field structures. Provide the TLD as a path parameter to retrieve its complete registration requirements. Useful when you only need details for one TLD (e.g., when a user selects .fr from a dropdown).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domainInfo.getRequirement({
    tld: "fr"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetRequirementRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainInfoClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domainInfo.<a href="/src/api/resources/domainInfo/client/Client.ts">checkDomainClaims</a>({ ...params }) -> Namecom.DomainClaimsCheckResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Performs the actual claims check for a specific domain. This endpoint checks if a specific domain has trademark claims against it, returning detailed information about any matching trademarks and their holders. Use this to verify if a domain can be registered without trademark conflicts. Please see the [claims flow](/guides/claims-flow) for information on how to use this endpoint in your domain purchase flow.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domainInfo.checkDomainClaims({
    domain: "tiktok.page"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.DomainClaimsCheckRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainInfoClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.domainInfo.<a href="/src/api/resources/domainInfo/client/Client.ts">getTldRequirementsV2</a>({ ...params }) -> Namecom.RequirementsJsonSchema</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns the registration requirements as a JSON Schema (Draft 7) document. This endpoint is designed for form generation and validation libraries that consume JSON Schema directly.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.domainInfo.getTldRequirementsV2({
    tld: "fr"
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.GetTldRequirementsV2Request` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `DomainInfoClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## TLD Pricing
<details><summary><code>client.tldPricing.<a href="/src/api/resources/tldPricing/client/Client.ts">tldPriceList</a>({ ...params }) -> Namecom.TldPriceListResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

This endpoint returns an alphabetical list of all TLDs supported by name.com, including pricing for each supported order type. All prices are in US Dollars (USD) and apply to non-premium domains. name.com provides three pricing types for each TLD:
- Account-Level Pricing - Your price, including any applicable rebates, promotions, or account-level discounts. This is referenced as 'registrationprice', 'renewalprice', 'transferinprice' and 'domainrestorationprice' in this endpoint.
- Original Pricing (No Discounts Applied) - The suggested retail price (MSRP) before any discounts are applied.
- Retail Pricing (Public Site Pricing) - The current public retail price on name.com, including any public rebates or promotions, but before any account-level discounts.

**Important Notes:**
- Promo codes are not supported through the API, and therefore are not reflected in any pricing values returned.
- General TLD pricing only: This represents standard pricing for domains registered under the specified TLD. Pricing for specific domains may differ based on multiple factors (e.g., premium classifications, registry pricing rules). To retrieve pricing for an individual domain, use the GetPricingForDomain endpoint.
- Availability: If a pricing value is returned as null, that product type is not currently supported for the TLD. (Example: registrationPrice = null means registrations are not currently available.)
- If you do not have account level pricing, the retail price will always match your account level price. (e.g., registration price = registration retail price)
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.tldPricing.tldPriceList({
    duration: 1
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.TldPriceListRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `TldPricingClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Premium Domains
<details><summary><code>client.premiumDomains.<a href="/src/api/resources/premiumDomains/client/Client.ts">premiumDomainLists</a>() -> Namecom.PremiumDomainsDownloadResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Gets a pre-signed URL that will allow a user to download a list of premium domains, with their registration and renewal pricing.
**Please Note:** The pre-signed URL will only be valid for 10 minutes. This endpoint is only available to approved reseller accounts. Contact name.com support to request access.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.premiumDomains.premiumDomainLists();

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**requestOptions:** `PremiumDomainsClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Contact Verification
<details><summary><code>client.contactVerification.<a href="/src/api/resources/contactVerification/client/Client.ts">unverifiedContactsList</a>({ ...params }) -> Namecom.UnverifiedContactsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Returns a list of contacts, related to domains within your account, that require verification as per ICANN procedures.
When a new domain is created, unverified contacts are not immediately available in API responses.  Records are added by a scheduled process that runs approximately every 10 minutes.  As a result, there may be up to a 10-minute delay before unverified contacts appear in the API. This delay also applies to related events such as webhooks or other downstream systems that depend on contact verification data. 
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.contactVerification.unverifiedContactsList({
    perPage: 100,
    page: 2
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.UnverifiedContactsListRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `ContactVerificationClient.RequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.contactVerification.<a href="/src/api/resources/contactVerification/client/Client.ts">verifyContact</a>({ ...params }) -> void</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Use this API to verify a contact.
This API is only available to approved reseller accounts. Contact name.com support to request access.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.contactVerification.verifyContact({
    verificationId: 1,
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.VerifyContactRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `ContactVerificationClient.IdempotentRequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.contactVerification.<a href="/src/api/resources/contactVerification/client/Client.ts">resendContactVerificationEmail</a>({ ...params }) -> Namecom.ContactVerificationResendResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Resend the contact verification email for a pending verification record.

### Throttling
This endpoint enforces strict throttling to prevent abuse:
- Per `verificationId`: max 1 resend per 15 minutes
- Per reseller account: max 200 resends per rolling hour

`nextEligibleAt` is always returned so the client knows when it can try again.

On `429`, the response uses the standard error envelope, and `details` contains the earliest retry time (RFC3339 UTC).
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```typescript
await client.contactVerification.resendContactVerificationEmail({
    verificationId: 1,
    body: {}
});

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `Namecom.ResendContactVerificationEmailRequest` 
    
</dd>
</dl>

<dl>
<dd>

**requestOptions:** `ContactVerificationClient.IdempotentRequestOptions` 
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

