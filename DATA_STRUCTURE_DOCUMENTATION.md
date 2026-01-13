# BontTree Strapi Data Structure Documentation

## Overview

This is a Strapi-based CMS for a cafe/restaurant menu management system. The system supports multi-language content (i18n), draft/publish workflows, and complex relationships between menu items, categories, tags, add-ons, and promotions.

## System Architecture

### Content Types

The system consists of:
- **5 Collection Types**: Tag, Category, Menu Item, Add-On, Promotion
- **2 Single Types**: Global Setting, About

### Key Features

- **Internationalization (i18n)**: Most content types support multiple languages
- **Draft & Publish**: Most collection types support draft/publish workflow
- **Media Management**: Images and files are stored via media fields
- **Component-based**: Reusable components for sizes, opening hours, SEO, etc.

---

## Collection Types

### 1. Tag

**Type**: Collection Type  
**Collection Name**: `tags`  
**Draft & Publish**: ✅ Enabled  
**i18n**: ✅ Enabled (all fields localized)

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Primary key (auto-generated) |
| `name` | String | ✅ Yes | - | Tag name (localized) |
| `slug` | UID | ✅ Yes | Generated from `name` | URL-friendly identifier (localized) |
| `icon` | String | ❌ No | Max 2 characters | Icon representation (localized) |
| `color` | Enumeration | ❌ No | Default: "gray" | Tag color (localized) |
| | | | Options: "orange", "blue", "green", "yellow", "purple", "gray", "indigo" | |
| `menuItems` | Relation | ❌ No | Many-to-Many | Related menu items (inverse of MenuItem.tags) |
| `createdAt` | DateTime | Auto | - | Creation timestamp |
| `updatedAt` | DateTime | Auto | - | Last update timestamp |
| `publishedAt` | DateTime | Auto | - | Publication timestamp (if published) |
| `locale` | String | Auto | - | Current locale |

#### Relationships

- **Many-to-Many** with `MenuItem` (via `menuItems` field)

#### API Endpoints

- `GET /api/tags` - List all tags
- `GET /api/tags/:id` - Get single tag
- `POST /api/tags` - Create tag
- `PUT /api/tags/:id` - Update tag
- `DELETE /api/tags/:id` - Delete tag

---

### 2. Category

**Type**: Collection Type  
**Collection Name**: `categories`  
**Draft & Publish**: ❌ Disabled  
**i18n**: ✅ Enabled (all fields localized)

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Primary key (auto-generated) |
| `name` | String | ✅ Yes | - | Category name (localized) |
| `slug` | UID | ✅ Yes | Generated from `name` | URL-friendly identifier |
| `description` | Text | ❌ No | - | Category description (localized) |
| `sortOrder` | Integer | ✅ Yes | Default: 0 | Display order (localized) |
| `isVisible` | Boolean | ✅ Yes | Default: true | Visibility flag (localized) |
| `icon` | String | ❌ No | Max 2 characters | Icon representation (localized) |
| `menu_items` | Relation | ❌ No | One-to-Many | Related menu items (inverse of MenuItem.category) |
| `createdAt` | DateTime | Auto | - | Creation timestamp |
| `updatedAt` | DateTime | Auto | - | Last update timestamp |
| `locale` | String | Auto | - | Current locale |

#### Relationships

- **One-to-Many** with `MenuItem` (via `menu_items` field)

#### API Endpoints

- `GET /api/categories` - List all categories
- `GET /api/categories/:id` - Get single category
- `POST /api/categories` - Create category
- `PUT /api/categories/:id` - Update category
- `DELETE /api/categories/:id` - Delete category

---

### 3. Menu Item

**Type**: Collection Type  
**Collection Name**: `menu_items`  
**Draft & Publish**: ✅ Enabled  
**i18n**: ✅ Enabled (most fields localized)

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Primary key (auto-generated) |
| `name` | String | ✅ Yes | - | Item name (localized) |
| `slug` | UID | ✅ Yes | Generated from `name` | URL-friendly identifier (localized) |
| `shortDescription` | String | ✅ Yes | Max 200 chars | Brief description (localized) |
| `longDescription` | RichText | ❌ No | - | Detailed description (localized) |
| `price` | Decimal | ✅ Yes | - | Base price for the item **Not localized** |
| `itemStatus` | Enumeration | ❌ No | Default: "active" | Item availability status |
| | | | Options: "active", "hidden", "sold_out", "scheduled" | **Not localized** |
| `isFeatured` | Boolean | ❌ No | Default: false | Featured item flag **Not localized** |
| `tags` | Relation | ❌ No | Many-to-Many | Related tags (inverse of Tag.menuItems) |
| `image` | Media | ✅ Yes | Single, images only | Item image **Not localized** |
| `availableTemperatures` | Enumeration | ❌ No | - | Available temperature options (localized) |
| | | | Options: "Hot", "Cold", "Hot & Cold" | |
| `allergenInfo` | Text | ❌ No | - | Allergen information (localized) |
| `availableFrom` | Date | ❌ No | - | Start date for availability **Not localized** |
| `availableUntil` | Date | ❌ No | - | End date for availability (localized) |
| `category` | Relation | ❌ No | Many-to-One | Parent category (inverse of Category.menu_items) |
| `addOns` | Relation | ❌ No | Many-to-Many | Available add-ons (inverse of AddOn.menuItems) |
| `createdAt` | DateTime | Auto | - | Creation timestamp |
| `updatedAt` | DateTime | Auto | - | Last update timestamp |
| `publishedAt` | DateTime | Auto | - | Publication timestamp (if published) |
| `locale` | String | Auto | - | Current locale |

#### Relationships

- **Many-to-Many** with `Tag` (via `tags` field)
- **Many-to-One** with `Category` (via `category` field)
- **Many-to-Many** with `AddOn` (via `addOns` field)
- **One-to-Many** with `Promotion` (via Promotion.items)

#### API Endpoints

- `GET /api/menu-items` - List all menu items
- `GET /api/menu-items/:id` - Get single menu item
- `POST /api/menu-items` - Create menu item
- `PUT /api/menu-items/:id` - Update menu item
- `DELETE /api/menu-items/:id` - Delete menu item

#### Query Examples

- Filter by category: `GET /api/menu-items?filters[category][id][$eq]=1`
- Filter by status: `GET /api/menu-items?filters[itemStatus][$eq]=active`
- Get featured items: `GET /api/menu-items?filters[isFeatured][$eq]=true`
- Populate relations: `GET /api/menu-items?populate=*`

---

### 4. Add-On

**Type**: Collection Type  
**Collection Name**: `add_ons`  
**Draft & Publish**: ✅ Enabled  
**i18n**: ✅ Enabled (all fields localized)

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Primary key (auto-generated) |
| `name` | String | ✅ Yes | - | Add-on name (localized) |
| `priceAdjustment` | Decimal | ✅ Yes | Min: 0 | Price change amount (localized) |
| `type` | Enumeration | ❌ No | - | Add-on type (localized) |
| | | | Options: "milk", "syrup", "extra_shot", "topping", "other" | |
| `description` | String | ❌ No | - | Add-on description (localized) |
| `isActive` | Boolean | ✅ Yes | Default: true | Active status (localized) |
| `menuItems` | Relation | ❌ No | Many-to-Many | Related menu items (inverse of MenuItem.addOns) |
| `createdAt` | DateTime | Auto | - | Creation timestamp |
| `updatedAt` | DateTime | Auto | - | Last update timestamp |
| `publishedAt` | DateTime | Auto | - | Publication timestamp (if published) |
| `locale` | String | Auto | - | Current locale |

#### Relationships

- **Many-to-Many** with `MenuItem` (via `menuItems` field)

#### API Endpoints

- `GET /api/add-ons` - List all add-ons
- `GET /api/add-ons/:id` - Get single add-on
- `POST /api/add-ons` - Create add-on
- `PUT /api/add-ons/:id` - Update add-on
- `DELETE /api/add-ons/:id` - Delete add-on

---

### 5. Promotion

**Type**: Collection Type  
**Collection Name**: `promotions`  
**Draft & Publish**: ✅ Enabled  
**i18n**: ✅ Enabled (most fields localized)

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Primary key (auto-generated) |
| `title` | String | ✅ Yes | - | Promotion title (localized) |
| `subtitle` | String | ❌ No | - | Promotion subtitle (localized) |
| `description` | RichText | ❌ No | - | Promotion description (localized) |
| `startDate` | Date | ❌ No | - | Promotion start date **Not localized** |
| `endDate` | Date | ❌ No | - | Promotion end date (localized) |
| `isActive` | Boolean | ✅ Yes | Default: true | Active status (localized) |
| `backgroundImage` | Media | ❌ No | Single, images only | Background image **Not localized** |
| `items` | Relation | ❌ No | One-to-Many | Related menu items |
| `createdAt` | DateTime | Auto | - | Creation timestamp |
| `updatedAt` | DateTime | Auto | - | Last update timestamp |
| `publishedAt` | DateTime | Auto | - | Publication timestamp (if published) |
| `locale` | String | Auto | - | Current locale |

#### Relationships

- **One-to-Many** with `MenuItem` (via `items` field)

#### API Endpoints

- `GET /api/promotions` - List all promotions
- `GET /api/promotions/:id` - Get single promotion
- `POST /api/promotions` - Create promotion
- `PUT /api/promotions/:id` - Update promotion
- `DELETE /api/promotions/:id` - Delete promotion

#### Query Examples

- Get active promotions: `GET /api/promotions?filters[isActive][$eq]=true`
- Get current promotions: `GET /api/promotions?filters[startDate][$lte]=2024-01-01&filters[endDate][$gte]=2024-01-01`

---

## Single Types

### 6. Global Setting

**Type**: Single Type  
**Collection Name**: `global_settings`  
**Draft & Publish**: ✅ Enabled  
**i18n**: ✅ Enabled (most fields localized)

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Primary key (auto-generated) |
| `cafeName` | String | ✅ Yes | - | Cafe/restaurant name (localized) |
| `tagline` | String | ❌ No | - | Business tagline (localized) |
| `logo` | Media | ❌ No | Single, all types | Business logo **Not localized** |
| `primaryColor` | String | ❌ No | - | Primary brand color **Not localized** |
| `secondaryColor` | String | ❌ No | - | Secondary brand color **Not localized** |
| `defaultCurrency` | String | ✅ Yes | Default: "EGP" | Default currency code (localized) |
| `currencySymbol` | String | ✅ Yes | Default: "EGP" | Currency symbol (localized) |
| `openingHours` | Component | ❌ No | Repeatable | Opening hours (see OpeningHours Component) (localized) |
| `createdAt` | DateTime | Auto | - | Creation timestamp |
| `updatedAt` | DateTime | Auto | - | Last update timestamp |
| `publishedAt` | DateTime | Auto | - | Publication timestamp (if published) |
| `locale` | String | Auto | - | Current locale |

#### Components Used

- `settings.opening-hours` (repeatable) - For opening hours

#### API Endpoints

- `GET /api/global-setting` - Get global settings (no ID needed for single types)
- `PUT /api/global-setting` - Update global settings
- `POST /api/global-setting` - Create/update global settings

---

### 7. About

**Type**: Single Type  
**Collection Name**: `abouts`  
**Draft & Publish**: ❌ Disabled  
**i18n**: ❌ Disabled

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Primary key (auto-generated) |
| `title` | String | ❌ No | - | About page title |
| `blocks` | DynamicZone | ❌ No | - | Flexible content blocks |
| | | | Components: "shared.media", "shared.quote", "shared.rich-text", "shared.slider" | |
| `createdAt` | DateTime | Auto | - | Creation timestamp |
| `updatedAt` | DateTime | Auto | - | Last update timestamp |

#### Components Used (Dynamic Zone)

- `shared.media` - Media block
- `shared.quote` - Quote block
- `shared.rich-text` - Rich text block
- `shared.slider` - Image slider block

#### API Endpoints

- `GET /api/about` - Get about page content
- `PUT /api/about` - Update about page
- `POST /api/about` - Create/update about page

---

## Components

### 1. Opening Hours Component

**Component Path**: `settings.opening-hours`  
**Collection Name**: `components_settings_opening_hours`

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Component instance ID |
| `day` | String | ✅ Yes | - | Day of the week |
| `open` | String | ✅ Yes | - | Opening time (format: "HH:mm" or similar) |
| `close` | String | ✅ Yes | - | Closing time (format: "HH:mm" or similar) |
| `isClosed` | Boolean | ❌ No | Default: false | Whether the day is closed |

#### Usage

Used in `GlobalSetting.openingHours` as a repeatable component to define weekly opening hours.

---

### 2. SEO Component

**Component Path**: `shared.seo`  
**Collection Name**: `components_shared_seos`

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Component instance ID |
| `metaTitle` | String | ✅ Yes | - | SEO meta title |
| `metaDescription` | Text | ✅ Yes | - | SEO meta description |
| `shareImage` | Media | ❌ No | Single, images only | Social sharing image |

#### Usage

This component can be used in any content type that requires SEO metadata.

---

### 3. Media Component

**Component Path**: `shared.media`  
**Collection Name**: `components_shared_media`

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Component instance ID |
| `file` | Media | ❌ No | Single, all types | Media file (image, video, or file) |

#### Usage

Used in `About.blocks` dynamic zone as a media block.

---

### 4. Quote Component

**Component Path**: `shared.quote`  
**Collection Name**: `components_shared_quotes`

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Component instance ID |
| `title` | String | ❌ No | - | Quote title/attribution |
| `body` | Text | ❌ No | - | Quote text |

#### Usage

Used in `About.blocks` dynamic zone as a quote block.

---

### 5. Rich Text Component

**Component Path**: `shared.rich-text`  
**Collection Name**: `components_shared_rich_texts`

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Component instance ID |
| `body` | RichText | ❌ No | - | Rich text content |

#### Usage

Used in `About.blocks` dynamic zone as a rich text block.

---

### 6. Slider Component

**Component Path**: `shared.slider`  
**Collection Name**: `components_shared_sliders`

#### Attributes

| Field | Type | Required | Constraints | Description |
|-------|------|----------|-------------|-------------|
| `id` | Integer | Auto | - | Component instance ID |
| `files` | Media | ❌ No | Multiple, images only | Slider images |

#### Usage

Used in `About.blocks` dynamic zone as an image slider block.

---

## Entity Relationship Diagram

```
┌─────────────┐
│   Category  │
└──────┬──────┘
       │ (one-to-many)
       │
       ▼
┌─────────────┐      ┌─────────────┐
│ Menu Item   │◄────►│     Tag     │
└──────┬──────┘      └─────────────┘
       │ (many-to-many)
       │
       │ (many-to-many)
       │
       ▼
┌─────────────┐
│   Add-On    │
└─────────────┘

┌─────────────┐
│  Promotion  │
└──────┬──────┘
       │ (one-to-many)
       │
       ▼
┌─────────────┐
│ Menu Item   │
└─────────────┘
```

---

## Internationalization (i18n)

### Localized Content Types

Most content types support i18n with the following pattern:
- Fields marked with `"localized": true` in their `pluginOptions.i18n` can have different values per locale
- Fields marked with `"localized": false` or without i18n config are shared across all locales

### Locale Handling in API

- Default locale queries: `GET /api/menu-items` (uses default locale)
- Specific locale: `GET /api/menu-items?locale=en` or `GET /api/menu-items?locale=ar`
- All locales: `GET /api/menu-items?locale=all`

### Non-Localized Fields

These fields are shared across all locales:
- `MenuItem.price`
- `MenuItem.itemStatus`
- `MenuItem.isFeatured`
- `MenuItem.availableFrom`
- `MenuItem.image`
- `GlobalSetting.logo`
- `GlobalSetting.primaryColor`
- `GlobalSetting.secondaryColor`
- `Promotion.startDate`
- `Promotion.backgroundImage`

---

## Draft & Publish Workflow

### Content Types with Draft/Publish

- ✅ Tag
- ✅ Menu Item
- ✅ Add-On
- ✅ Promotion
- ✅ Global Setting

### Content Types without Draft/Publish

- ❌ Category
- ❌ About

### API Behavior

- **Draft content**: Only accessible via admin panel or with special permissions
- **Published content**: Accessible via public API endpoints
- Query drafts: `GET /api/menu-items?publicationState=preview`
- Query published: `GET /api/menu-items?publicationState=live` (default)

---

## Media Fields

### Media Field Types

1. **Single Media**: `"multiple": false`
   - Returns: Single media object or null
   - Example: `MenuItem.image`

2. **Multiple Media**: `"multiple": true`
   - Returns: Array of media objects
   - Example: `Slider.files`

### Media Allowed Types

- `"images"` - Image files only
- `"files"` - Document files
- `"videos"` - Video files
- `"audios"` - Audio files
- Array of types: `["images", "files", "videos"]` - Multiple types allowed

### Media Object Structure

```json
{
  "id": 1,
  "name": "image.jpg",
  "alternativeText": "Alt text",
  "caption": "Caption",
  "width": 1920,
  "height": 1080,
  "formats": {
    "thumbnail": { "url": "...", "width": 150, "height": 150 },
    "small": { "url": "...", "width": 500, "height": 500 },
    "medium": { "url": "...", "width": 750, "height": 750 },
    "large": { "url": "...", "width": 1000, "height": 1000 }
  },
  "hash": "...",
  "ext": ".jpg",
  "mime": "image/jpeg",
  "size": 123456,
  "url": "https://...",
  "previewUrl": null,
  "provider": "cloudflare-r2",
  "provider_metadata": null,
  "createdAt": "2024-01-01T00:00:00.000Z",
  "updatedAt": "2024-01-01T00:00:00.000Z"
}
```

---

## API Response Structure

### Collection Type Response

```json
{
  "data": [
    {
      "id": 1,
      "attributes": {
        "name": "Item Name",
        "slug": "item-name",
        "createdAt": "2024-01-01T00:00:00.000Z",
        "updatedAt": "2024-01-01T00:00:00.000Z",
        "publishedAt": "2024-01-01T00:00:00.000Z",
        "locale": "en"
      }
    }
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "pageSize": 25,
      "pageCount": 1,
      "total": 1
    }
  }
}
```

### Single Type Response

```json
{
  "data": {
    "id": 1,
    "attributes": {
      "cafeName": "BontTree Cafe",
      "createdAt": "2024-01-01T00:00:00.000Z",
      "updatedAt": "2024-01-01T00:00:00.000Z",
      "publishedAt": "2024-01-01T00:00:00.000Z",
      "locale": "en"
    }
  }
}
```

### Populated Relations

When using `?populate=*` or specific fields:

```json
{
  "data": {
    "id": 1,
    "attributes": {
      "name": "Coffee",
      "category": {
        "data": {
          "id": 1,
          "attributes": {
            "name": "Beverages"
          }
        }
      },
      "tags": {
        "data": [
          {
            "id": 1,
            "attributes": {
              "name": "Hot"
            }
          }
        ]
      }
    }
  }
}
```

---

## Field Type Reference

### String
- Basic text field
- Max length can be specified (e.g., `maxLength: 200`)

### Text
- Multi-line text field
- No length limit

### RichText
- Rich text editor content (HTML/Markdown)
- Stored as structured content

### Integer
- Whole numbers
- Can have `min` and `max` constraints
- Can have `default` value

### Decimal
- Floating-point numbers
- Can have `min` and `max` constraints
- Used for prices

### Boolean
- True/false values
- Can have `default` value

### Date
- Date only (no time)
- Format: ISO 8601 date string

### DateTime
- Date and time
- Auto-generated for `createdAt`, `updatedAt`, `publishedAt`
- Format: ISO 8601 datetime string

### Enumeration
- Predefined list of values
- Must match one of the enum values

### UID
- Unique identifier field
- Auto-generated from `targetField`
- URL-friendly slugs

### Media
- File upload field
- Can be single or multiple
- Can restrict to specific file types

### Relation
- Links to other content types
- Types: oneToOne, oneToMany, manyToOne, manyToMany

### Component
- Reusable component
- Can be single or repeatable

### DynamicZone
- Flexible content area
- Can contain multiple component types

---

## API Query Parameters

### Filtering

```
GET /api/menu-items?filters[name][$eq]=Coffee
GET /api/menu-items?filters[price][$gt]=10
GET /api/menu-items?filters[itemStatus][$in][0]=active&filters[itemStatus][$in][1]=scheduled
```

### Sorting

```
GET /api/menu-items?sort=name:asc
GET /api/menu-items?sort=createdAt:desc
GET /api/menu-items?sort[0]=category.sortOrder:asc&sort[1]=name:asc
```

### Pagination

```
GET /api/menu-items?pagination[page]=1&pagination[pageSize]=10
GET /api/menu-items?pagination[start]=0&pagination[limit]=25
```

### Population

```
GET /api/menu-items?populate=*
GET /api/menu-items?populate[category]=*
GET /api/menu-items?populate[category][populate]=*
GET /api/menu-items?populate[0]=category&populate[1]=tags
```

### Fields Selection

```
GET /api/menu-items?fields[0]=name&fields[1]=slug&fields[2]=price
```

### Locale

```
GET /api/menu-items?locale=en
GET /api/menu-items?locale=ar
GET /api/menu-items?locale=all
```

### Publication State

```
GET /api/menu-items?publicationState=live
GET /api/menu-items?publicationState=preview
```

---

## Recommended API Endpoints Structure

### Public API Endpoints

```
GET    /api/menu-items              # List all published menu items
GET    /api/menu-items/:id         # Get single menu item
GET    /api/categories              # List all categories
GET    /api/categories/:id          # Get single category
GET    /api/tags                    # List all tags
GET    /api/tags/:id                # Get single tag
GET    /api/add-ons                 # List all published add-ons
GET    /api/add-ons/:id             # Get single add-on
GET    /api/promotions              # List all active promotions
GET    /api/promotions/:id          # Get single promotion
GET    /api/global-setting          # Get global settings
GET    /api/about                   # Get about page
```

### Admin API Endpoints (require authentication)

```
POST   /api/menu-items              # Create menu item
PUT    /api/menu-items/:id          # Update menu item
DELETE /api/menu-items/:id          # Delete menu item
# ... similar for all collection types
```

### Specialized Endpoints (recommended)

```
GET    /api/menu-items/featured     # Get featured items
GET    /api/menu-items/by-category/:categoryId  # Get items by category
GET    /api/menu-items/by-tag/:tagId            # Get items by tag
GET    /api/promotions/active       # Get currently active promotions
GET    /api/categories/visible      # Get visible categories only
```

---

## Data Validation Rules

### Required Fields

- Tag: `name`, `slug`
- Category: `name`, `slug`, `sortOrder`, `isVisible`
- Menu Item: `name`, `slug`, `shortDescription`, `image`, `price`
- Add-On: `name`, `priceAdjustment`, `isActive`
- Promotion: `title`, `isActive`
- Global Setting: `cafeName`, `defaultCurrency`, `currencySymbol`
- SEO Component: `metaTitle`, `metaDescription`
- Opening Hours Component: `day`, `open`, `close`

### Constraints

- `shortDescription`: Max 200 characters
- `icon`: Max 2 characters (for Tag and Category)
- `priceAdjustment`: Min 0 (for Add-On)
- `price`: Min 0 (for Size component)
- `color`: Must be one of: "orange", "blue", "green", "yellow", "purple", "gray", "indigo"
- `itemStatus`: Must be one of: "active", "hidden", "sold_out", "scheduled"
- `availableTemperatures`: Must be one of: "Hot", "Cold", "Hot & Cold"
- `type` (Add-On): Must be one of: "milk", "syrup", "extra_shot", "topping", "other"

---

## Best Practices for API Implementation

1. **Always populate relations** when fetching menu items to get complete data
2. **Filter by publication state** to ensure only published content is returned publicly
3. **Respect locale settings** - provide locale parameter support
4. **Handle media URLs** - ensure proper URL generation for media fields
5. **Validate enum values** - ensure enum fields match allowed values
6. **Handle date ranges** - for promotions and availability windows
7. **Sort categories** by `sortOrder` for proper display order
8. **Filter visible categories** using `isVisible` flag
9. **Handle component arrays** - opening hours are arrays
10. **Support dynamic zones** - About page blocks can be any of 4 component types

---

## Example API Responses

### Complete Menu Item with Relations

```json
{
  "data": {
    "id": 1,
    "attributes": {
      "name": "Espresso",
      "slug": "espresso",
      "shortDescription": "Strong Italian coffee",
      "longDescription": "<p>Rich and bold espresso...</p>",
      "price": 25.00,
      "itemStatus": "active",
      "isFeatured": true,
      "image": {
        "data": {
          "id": 1,
          "attributes": {
            "url": "/uploads/espresso.jpg",
            "width": 800,
            "height": 600
          }
        }
      },
      "availableTemperatures": ["Hot"],
      "allergenInfo": "Contains caffeine",
      "category": {
        "data": {
          "id": 1,
          "attributes": {
            "name": "Beverages",
            "slug": "beverages"
          }
        }
      },
      "tags": {
        "data": [
          {
            "id": 1,
            "attributes": {
              "name": "Hot",
              "color": "orange"
            }
          }
        ]
      },
      "addOns": {
        "data": [
          {
            "id": 1,
            "attributes": {
              "name": "Extra Shot",
              "priceAdjustment": 5.00,
              "type": "extra_shot"
            }
          }
        ]
      },
      "createdAt": "2024-01-01T00:00:00.000Z",
      "updatedAt": "2024-01-01T00:00:00.000Z",
      "publishedAt": "2024-01-01T00:00:00.000Z",
      "locale": "en"
    }
  }
}
```

### Global Settings with Opening Hours

```json
{
  "data": {
    "id": 1,
    "attributes": {
      "cafeName": "BontTree Cafe",
      "tagline": "Fresh Coffee, Every Day",
      "logo": {
        "data": {
          "id": 1,
          "attributes": {
            "url": "/uploads/logo.png"
          }
        }
      },
      "primaryColor": "#8B4513",
      "secondaryColor": "#D2691E",
      "defaultCurrency": "EGP",
      "currencySymbol": "£",
      "openingHours": [
        {
          "id": 1,
          "day": "Monday",
          "open": "08:00",
          "close": "22:00",
          "isClosed": false
        },
        {
          "id": 2,
          "day": "Tuesday",
          "open": "08:00",
          "close": "22:00",
          "isClosed": false
        }
      ],
      "createdAt": "2024-01-01T00:00:00.000Z",
      "updatedAt": "2024-01-01T00:00:00.000Z",
      "publishedAt": "2024-01-01T00:00:00.000Z",
      "locale": "en"
    }
  }
}
```

---

## Summary

This Strapi CMS manages a cafe/restaurant menu system with:

- **7 Content Types**: 5 collection types (Tag, Category, Menu Item, Add-On, Promotion) and 2 single types (Global Setting, About)
- **6 Reusable Components**: Opening Hours, SEO, Media, Quote, Rich Text, Slider
- **Complex Relationships**: Many-to-many (Menu Items ↔ Tags, Menu Items ↔ Add-Ons), One-to-many (Category → Menu Items, Promotion → Menu Items)
- **Multi-language Support**: Most content types support i18n
- **Draft/Publish Workflow**: Most collection types support content staging
- **Media Management**: Images, files, videos supported with automatic format generation
- **Flexible Content**: Dynamic zones for flexible page layouts

The API should support filtering, sorting, pagination, population of relations, locale selection, and publication state management to provide a complete interface for frontend applications.

