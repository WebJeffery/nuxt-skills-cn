# Schema.org 结构��数据类型

## 什么是结构化数据?

结构化数据是标准化的数据格式,帮助搜索引擎更好地理解网页内容。正确的结构化数据可以在搜索结果中显示丰富摘要(Rich Snippets)。

## 常用结构化数据类型

### 1. WebPage(网页)

最基本的网页类型。

```typescript
useSchemaOrg([
  defineWebPage({
    name: '页面名称',
    description: '页面描述',
    url: 'https://example.com/page',
  }),
])
```

### 2. Article(文章)

用于博客文章、新闻文章等。

```typescript
useSchemaOrg([
  defineArticle({
    '@type': 'Article',
    headline: '文章标题',
    description: '文章描述',
    image: 'https://example.com/image.jpg',
    datePublished: '2024-01-01T10:00:00+08:00',
    dateModified: '2024-01-02T15:30:00+08:00',
    author: [
      {
        '@type': 'Person',
        name: '作者名称',
        url: 'https://example.com/author',
      },
    ],
    publisher: {
      '@type': 'Organization',
      name: '发布者名称',
      logo: {
        '@type': 'ImageObject',
        url: 'https://example.com/logo.jpg',
      },
    },
    mainEntityOfPage: {
      '@type': 'WebPage',
      '@id': 'https://example.com/article',
    },
  }),
])
```

**Article 子类型:**
- `BlogPosting` - 博客文章
- `NewsArticle` - 新闻文章
- `TechArticle` - 技术文章
- `Report` - 报告

### 3. Product(产品)

电商产品页面。

```typescript
useSchemaOrg([
  defineProduct({
    name: '产品名称',
    description: '产品描述',
    image: [
      'https://example.com/product-1.jpg',
      'https://example.com/product-2.jpg',
    ],
    sku: 'PRODUCT-SKU-001',
    gtin: 'GTIN-CODE',
    brand: {
      '@type': 'Brand',
      name: '品牌名称',
    },
    offers: {
      '@type': 'Offer',
      url: 'https://example.com/product',
      priceCurrency: 'CNY',
      price: '299.00',
      priceValidUntil: '2024-12-31',
      availability: 'https://schema.org/InStock',
      itemCondition: 'https://schema.org/NewCondition',
    },
    aggregateRating: {
      '@type': 'AggregateRating',
      ratingValue: '4.8',
      reviewCount: '128',
      bestRating: '5',
      worstRating: '1',
    },
  }),
])
```

### 4. Organization(组织)

公司或组织信息。

```typescript
useSchemaOrg([
  defineOrganization({
    name: '公司名称',
    url: 'https://example.com',
    logo: 'https://example.com/logo.png',
    description: '公司描述',
    address: {
      '@type': 'PostalAddress',
      streetAddress: '街道地址',
      addressLocality: '城市',
      addressRegion: '省份',
      postalCode: '邮编',
      addressCountry: 'CN',
    },
    contactPoint: {
      '@type': 'ContactPoint',
      telephone: '+86-400-xxx-xxxx',
      contactType: 'customer service',
      availableLanguage: ['Chinese', 'English'],
    },
    sameAs: [
      'https://twitter.com/company',
      'https://facebook.com/company',
      'https://linkedin.com/company',
    ],
  }),
])
```

### 5. Person(人物)

作者、创作者等个人信息。

```typescript
useSchemaOrg([
  definePerson({
    name: '张三',
    url: 'https://example.com/author/zhangsan',
    image: 'https://example.com/avatar.jpg',
    jobTitle: '技术作家',
    worksFor: {
      '@type': 'Organization',
      name: '公司名称',
    },
    sameAs: [
      'https://twitter.com/zhangsan',
      'https://github.com/zhangsan',
    ],
  }),
])
```

### 6. BreadcrumbList(面包屑导航)

面包屑导航结构化数据。

```typescript
useSchemaOrg([
  defineBreadcrumb({
    itemListElement: [
      {
        '@type': 'ListItem',
        position: 1,
        name: '首页',
        item: 'https://example.com',
      },
      {
        '@type': 'ListItem',
        position: 2,
        name: '产品',
        item: 'https://example.com/products',
      },
      {
        '@type': 'ListItem',
        position: 3,
        name: 'iPhone 15',
        item: 'https://example.com/products/iphone-15',
      },
    ],
  }),
])
```

### 7. Review(评论)

产品或服务的评论。

```typescript
useSchemaOrg([
  defineReview({
    '@type': 'Review',
    itemReviewed: {
      '@type': 'Product',
      name: '产品名称',
    },
    reviewRating: {
      '@type': 'Rating',
      ratingValue: '5',
      bestRating: '5',
    },
    author: {
      '@type': 'Person',
      name: '评论者姓名',
    },
    reviewBody: '评论内容...',
    datePublished: '2024-01-01',
  }),
])
```

### 8. FAQPage(常见问题)

FAQ 页面结构化数据。

```typescript
useSchemaOrg([
  defineQuestion({
    '@type': 'Question',
    name: '问题1?',
    acceptedAnswer: {
      '@type': 'Answer',
      text: '答案1...',
    },
  }),
  defineQuestion({
    '@type': 'Question',
    name: '问题2?',
    acceptedAnswer: {
      '@type': 'Answer',
      text: '答案2...',
    },
  }),
])
```

### 9. Event(活动)

活动、会议、演出等。

```typescript
useSchemaOrg([
  defineEvent({
    '@type': 'Event',
    name: '活动名称',
    description: '活动描述',
    startDate: '2024-06-01T09:00:00+08:00',
    endDate: '2024-06-01T17:00:00+08:00',
    location: {
      '@type': 'Place',
      name: '活动地点',
      address: {
        '@type': 'PostalAddress',
        streetAddress: '地址',
        addressLocality: '城市',
        addressCountry: 'CN',
      },
    },
    image: 'https://example.com/event.jpg',
    offers: {
      '@type': 'Offer',
      url: 'https://example.com/tickets',
      price: '199',
      priceCurrency: 'CNY',
      availability: 'https://schema.org/InStock',
    },
    organizer: {
      '@type': 'Organization',
      name: '组织者名称',
      url: 'https://example.com',
    },
  }),
])
```

### 10. VideoObject(视频)

视频内容。

```typescript
useSchemaOrg([
  defineVideoObject({
    '@type': 'VideoObject',
    name: '视频标题',
    description: '视频描述',
    thumbnailUrl: 'https://example.com/thumbnail.jpg',
    uploadDate: '2024-01-01T10:00:00+08:00',
    duration: 'PT10M30S', // 10分30秒
    contentUrl: 'https://example.com/video.mp4',
    embedUrl: 'https://example.com/embed/video',
    author: {
      '@type': 'Person',
      name: '作者名称',
    },
  }),
])
```

## 在 Nuxt 中使用

### 组合多个类型

```typescript
useSchemaOrg([
  defineWebPage({
    name: '产品详情页',
  }),
  defineProduct({
    name: 'iPhone 15',
    image: 'https://example.com/iphone15.jpg',
    offers: {
      '@type': 'Offer',
      price: '5999',
      priceCurrency: 'CNY',
    },
  }),
  defineBreadcrumb({
    itemListElement: [
      { position: 1, name: '首页', item: 'https://example.com' },
      { position: 2, name: 'iPhone', item: 'https://example.com/iphone' },
      { position: 3, name: 'iPhone 15' },
    ],
  }),
  defineReview({
    '@type': 'Review',
    reviewRating: { ratingValue: '4.8' },
    author: { '@type': 'Person', name: '用户名' },
  }),
])
```

### 动态生成结构化数据

```vue
<script setup lang="ts">
const { data: product } = await useFetch('/api/products/1')

if (product.value) {
  useSchemaOrg([
    defineProduct({
      name: product.value.name,
      description: product.value.description,
      image: product.value.images,
      offers: {
        '@type': 'Offer',
        price: product.value.price,
        priceCurrency: 'CNY',
        availability: product.value.inStock
          ? 'https://schema.org/InStock'
          : 'https://schema.org/OutOfStock',
      },
    }),
  ])
}
</script>
```

## 验证工具

- [Google Rich Results Test](https://search.google.com/test/rich-results)
- [Schema Markup Validator](https://validator.schema.org/)
- [Google Search Console](https://search.google.com/search-console)

## 最佳实践

1. **选择正确的类型** - 使用最符合内容类型的 Schema
2. **必填字段** - 确保所有必填字段都有值
3. **数据准确** - 结构化数据必须与页面内容一致
4. **避免滥用** - 不要为了 SEO 添加与内容无关的结构化数据
5. **定期测试** - 使用验证工具确保格式正确

## 完整示例

产品页面完整结构化数据:

```vue
<script setup lang="ts">
const { data: product } = await useFetch('/api/products/1')

if (product.value) {
  useSchemaOrg([
    defineWebPage({
      name: product.value.name,
      description: product.value.description,
    }),
    defineProduct({
      name: product.value.name,
      description: product.value.description,
      image: product.value.images,
      sku: product.value.sku,
      brand: {
        '@type': 'Brand',
        name: product.value.brand,
      },
      offers: {
        '@type': 'Offer',
        url: `https://example.com/product/${product.value.id}`,
        priceCurrency: 'CNY',
        price: product.value.price.toString(),
        availability: product.value.inStock
          ? 'https://schema.org/InStock'
          : 'https://schema.org/OutOfStock',
      },
      aggregateRating: product.value.rating
        ? {
            '@type': 'AggregateRating',
            ratingValue: product.value.rating.toString(),
            reviewCount: product.value.reviewCount.toString(),
          }
        : undefined,
    }),
    defineBreadcrumb({
      itemListElement: [
        { position: 1, name: '首页', item: 'https://example.com' },
        { position: 2, name: '产品', item: 'https://example.com/products' },
        { position: 3, name: product.value.category, item: `https://example.com/products/${product.value.category}` },
        { position: 4, name: product.value.name },
      ],
    }),
  ])
}
</script>
```

## 参考

- [Schema.org 官方文档](https://schema.org/)
- [Google 搜索中心 - 结构化数据](https://developers.google.com/search/docs/advanced/structured-data/intro-structured-data)
