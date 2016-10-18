# Mule in the Dust

> It's Lackey implementation for DustJS as Express renderer.

We used to use [adaro](https://github.com/krakenjs/adaro). Anyhow we found it doesn't fully match our case. We decided to develop our own solution.

## POM 2.0

### POMObject

```yaml
_type: < 'Block' | 'List' | 'Variant' >
_props: < PropertiesMap >
_meta: < MetaMap >
<any> : < POMObject | String | Number >
```

#### Example

```yaml
_type: Block
_meta:
    route: /blog/my-awesome-layout
    created: 1476784049
    updated: 1476784049
    createdBy: UserObject
    template: TemplateObject
_props:
    author: UserObject # one you can set manually
    theme: red
title: My awesome layout
blocks:
    type: List
    items:
        -
            type: Block
            _props:
                theme: blue
            copy: |
                My awesome layout is more awesome than you are
            example:
                type: Variant
                *: Example
                *:*:pl: Przykład
```

Restricted names:

 * any starting with underscore

### PropertiesMap

```yaml
<any> : < String | Number | Boolean | Variant >
```

### MetaMap

```yaml
<any> : < String | Number | Boolean >
```

#### POM universal meta

| Name       | Type          | Description                                    | Example
| ---------- | ------------- | ---------------------------------------------- | -------
| route      | string        | canonical route to resource in the system      | /blog/my-awesome-layout
| uri        | string        | full uri as entered                            | https://example.com/de-DE/blog/my-awesome-layout
| cannonical | string        | canonical absolute uri                         | https://example.com/blog/my-awesome-layout
| locale     | string        | locale extracted from uri or session           | de-DE
| base       | stirng        | required for ensuring links are aboslute       | https://example.com/de-DE/

#### Lackey specific meta

| Name       | Type          | Description                                    | Example     | Values
| ---------- | ------------- | ---------------------------------------------- | ----------- | ----------------
| state      | enum          | Shows state of block                           | published   | draft, published |
| id         | number        | Unique id in the database                      | 134         | |
| created    | number        | Unixtimestamp of record creation               | 1476784049  | |
| updated    | number        | Unixtimestamp of record latest update          | 1476784049  | |
| publish    | number        | Unixtimestamp of record embargo                | 1476784049  | |
| author     | UserObject    | Actual user who has created the record         |             | |

### Mapping POM to POM 2.0

| POM                                   | POM 2.0
| ------------------------------------- | -------
| data.route                            | _meta.cannonical
| data.content.id                       | _meta.id
| data.content.$uri                     | TBD
| data.content.type                     | _type
| data.content.name                     | title
| data.content.route                    | _meta.uri
| data.content.createdAt                | _meta.created
| data.content.publishAt                | _meta.publish
| data.content.props.og_title           | title
| data.content.props.og_url             | _meta.cannonical
| data.content.author                   | _meta.author
| data.content.author (editable)        | _props.author
| data.content.template                 | _meta.template
| data.content.state                    | _meta.state
| data.content.layout                   | $
| data.content.[populated]              | [populated]
| data.content.taxonomies               | _taxonomies
| template                              | _meta.template.name
| [property]                            | [property]
| stylesheets                           | _meta.stylesheets
| javascript                            | _meta.javascripts
| edit                                  | _edit
| fragment                              | _fragment
| locale                                | _meta.locale
| host                                  | _meta.host
| env                                   | _meta.env
| defaultLocale                         | _meta.defaultLocale
| route                                 | _meta.route
| session                               | _session
| post                                  | TBD
| query                                 | TBD

### Title and OG:Title

In previous version of POM we had huge confusion around title and name of the page.

 * `$.data.content.name`
 * `$.data.content.props.og_title`
 * `$.data.content.layout.title`
 * any other

At the moment we will have just `$.title` which can (but don't have to) use variant `og`. Same goes for image and description. It should be edited via variant (i.e. `how looks on facebook view`).



### Breaking changes

 * New data layout
 * `Fields` type dies, page is a block too
 * (Lackey feature) autopopulated fields from template should land in root of POMObject, not in `$.data`