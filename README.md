# SOP: Displaying Non-Existent Pages in the Hamburger Menu

## Purpose
This document provides steps to add pages to OMS that do not show on the menu but have valid URLs, and a guide for pages where data is present in OMS, but still page is still not visible to OMS.

For example, the Shopify Solr Search page is useful for verifying updates through GraphQL and debugging API request errors. Since this page is not directly visible in OMS, we will add it under the relevant section in the hamburger menu.

## Note
- Please verify that the user has the necessary [permissions](https://docs.hotwax.co/documents/system-admins/administration/introduction/users-app#permissions-tab) to access the page.
- Additionally, check if a "thru date" is set on the `content assoc` or `security group permissions` entity, as this could be preventing the page from being visible in OMS.
- If a "thru date" is applied, the permission will expire once the date is reached. To resolve this, remove the "thru date" from the `content association` or `security group permissions`.
- If the page is still not visible after removing the "thru date," please ensure that all the below-required data is present in the database.

Creating any page requires a data resource, content, content association, and permission to view the page. Lastly, it involves a security permissions group where we can define which group can view its page.

Perform these steps in your local/test/dev instance first before applying changes to a client instance.

---

## Steps to Add a Non-Existent Page in OMS

### Step 1: Verify Data Availability
Check if the page data is present in the appropriate file:

- Ensure you have access to the git repository: Hotwax OMS Git Repository
- **Shopify-related pages:** `ShopifyData.xml`
- **Other pages:** `HamburgerData.xml`

🔗 **Reference Files:**
- `ShopifyData.xml`
- `HamburgerMenusData.xml`

Ensure that the required `objectInfo` exists in these files, as it specifies the location of external content.

### Step 2: Configure Data Resources & Content Associations

#### Add Data Resource
Define the data resource with a unique identifier and screen URL name.

The **Data Resource** entity stores actual content data (text, URLs, binary files like images and PDFs).

**Key Fields:**
- `dataResourceId` → Unique identifier for the data.
- `dataResourceTypeId` → Type of data (e.g., electronic text, link, image).
- `dataTemplateTypeId` → Template format (optional).
- `objectInfo` → Defines the location of external content, such as a URL pointing to an image or document.

```xml
<DataResource dataResourceId="SHOPIFY_SOLR_SRCH"
              dataResourceTypeId="LINK"
              dataTemplateTypeId="NONE"
              objectInfo="SHOPIFY_SOLR_SEARCH"/>
```

#### Create Content and Link to Menu
Defines relationships between different content items (e.g., linking an image to an article).

**Key Fields:**
- `contentId` → Unique identifier for content.
- `contentTypeId` → Categorizes content into types (e.g., Article, Blog, Image, Document).
- `statusId` → Status of content (e.g., Approved, Draft).
- `contentName` → Name of the content.
- `description` → Short description.
- `dataResourceId` → Links to DataResource, which stores the actual data.
- `permissionEnumId` → Short description.

```xml
<Content contentId="SHOPIFY_SOLR_SRCH"
         contentTypeId="DOCUMENT"
         dataResourceId="SHOPIFY_SOLR_SRCH"
         contentName="Shopify Solr Search"
         permissionEnumId="SHOPIFY"/>
```

#### Content Association
A **Content Association** defines relationships between content items, such as linking a submenu item to a main menu.

**Key Fields:**
- `contentId` → Primary content item.
- `contentIdTo` → Related content item.
- `contentAssocTypeId` → Type of relationship (e.g., "includes", "related to").
- `fromDate`, `thruDate` → Validity period of the association.

📌 **Note:** We have multiple menus; ensure you add your page to the correct menu. In this example, we are adding Shopify Solr Search to the Shopify menu.

```xml
<ContentAssoc contentId="SHOPIFY_MENU"
              contentIdTo="SHOPIFY_SOLR_SRCH"
              mapKey="sub_menu_item"
              contentAssocTypeId="SUB_CONTENT"
              fromDate="2025-02-06 01:01:01.000"
              sequenceNum="00"/>
```

✅ **Ensure the referenced screen exists for the page you are adding.**

### Step 3: Define Permissions

#### Create a Security Permission
Security permissions control who can view or interact with the page. If permissions are not added, users will not be able to see the page, even if it exists in the menu.

```xml
<SecurityPermission description="View Shopify Solr Search Page"
                    permissionId="SHOPIFY_SOLR_SEARCH"/>
```

#### Assign Permission to the Security Group
- **For Hotwax internal users:** Add permission to `SUPER` Group
- **For clients:** Add permission to `COMMERCE_SUPER` or `MERCHANDISE_MGR`

```xml
<SecurityGroupPermission fromDate="2025-02-06 06:22:36.783"
                         groupId="SUPER"
                         permissionId="SHOPIFY_SOLR_SEARCH"/>
```

---

## Verification
After completing these steps, confirm that the page appears in OMS.

If the page is not visible, review `objectInfo` and security permissions.

### Notes:
- This SOP applies to pages with valid URLs but no physical presence in OMS.
- If issues arise, consult a senior developer or engineer at HotWax Commerce.
