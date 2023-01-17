# hmd-tmpl-vocabgen

Base set of templates for Language Pack code generation using `hmd-cli-mickey`.
The templates and configurations in this repository are designed to be used with a standard NeuronSphere Language Pack project.
NeuronSphere Language Pack projects form core of all microservices deployed in a NeuronSphere environment.
A Language Pack consists of simple JSON files with an `.hms` extension in the `./src/schemas` folder.
They allow a developer to model a data domain for a microservice in terms of Nouns and Relationships.

Example Schema Files:

```json
{
  "name": "information_field",
  "namespace": "hmd_lang_semantic",
  "metatype": "noun",
  "attributes": {
    "global_id": {
      "type": "string",
      "business_key": true
    },
    "description": {
      "type": "string"
    }
  }
}
```

```json
{
  "name": "information_field_references_tabular_column",
  "namespace": "hmd_lang_semantic",
  "metatype": "relationship",
  "ref_from": "hmd_lang_semantic.information_field",
  "ref_to": "hmd_lang_structure.tabular_column",
  "attributes": {}
}
```

Both Nouns and Relationships may contain any number of attributes.
They can also be namespaced to prevent potential naming conflicts.

Given any number of valid schema files, it will produce the following artifacts:

- Python library and client
- Typescript library
- PlantUML class diagram
- SQL to create views from a standard NeuronSphere Microservice Postgres database
- Rego policy template for basic NeuronSphere CRUD operations

## Base Configuration

The configuration file `./src/mickey/templates/base_config.json` is designed to produce all the above artifacts with minimal configuration in the Language Pack.

Just add the following to your `manifest.json` in a Language Pack project.

```json
{
  "generate": {
    "base_vocabgen": {
      "config": "ns:hmd-tmpl-vocabgen@<VERSION>:build:src/mickey/templates/base_config.json"
    }
  },
  "global_variables": {
    "base_package": "<PROJECT NAME>",
    "package_name": "<PROJECT NAME>"
  }
}
```

Replace `<VERSION>` with the desired version of this repository to use, and `<PROJECT_NAME>` with the name of the Language Pack project replacing `-` with `_`.
This is the same as individually configuring all the options below.

## Configurations

### Python Library

```json
{
  "generate": {
    "base_vocabgen": {
      "config": "ns:hmd-tmpl-vocabgen@<VERSION>:build:src/mickey/templates/python_lib_config.json"
    }
  },
  "global_variables": {
    "base_package": "<PROJECT NAME>",
    "package_name": "<PROJECT NAME>"
  }
}
```

This generates a bare bones Python library for the language pack.
It includes a Python class for each Noun and Relationship based on classes found in `hmd-meta-types`.

### Python Client

```json
{
  "generate": {
    "base_vocabgen": {
      "config": "ns:hmd-tmpl-vocabgen@<VERSION>:build:src/mickey/templates/python_client_config.json"
    }
  },
  "global_variables": {
    "base_package": "<PROJECT NAME>",
    "package_name": "<PROJECT NAME>"
  }
}
```

This generates one Python file contianing a client class for retrieving and persisting the Language Pack entities to different storage engines.
It depends on the `DefaultLoader` class found in `hmd-schema-loader`, and the `BaseClient` class found in `hmd-graphql-client`.
You must pass a derived instance of `BaseClient` to the generated class's `__init__` method to use.
Example classes derived from `BaseClient` include `RestClient` for calling RESTful API endpoints, and `DbEngineClient` for querying a RDS directly.
More derived classes can be found in `hmd-graphql-client`.

### Typescript Library

```json
{
  "generate": {
    "base_vocabgen": {
      "config": "ns:hmd-tmpl-vocabgen@<VERSION>:build:src/mickey/templates/typescript_lib_config.json"
    }
  },
  "global_variables": {
    "base_package": "<PROJECT NAME>",
    "package_name": "<PROJECT NAME>"
  }
}
```

This generates an `index.ts` file containing an interface and class for each entity.
The classes allow you to interact with entities as strongly typed Typescript classes instead of plain JSON objects.

### Class PUML

```json
{
  "generate": {
    "base_vocabgen": {
      "config": "ns:hmd-tmpl-vocabgen@<VERSION>:build:src/mickey/templates/class_puml_config.json"
    }
  },
  "global_variables": {
    "base_package": "<PROJECT NAME>",
    "package_name": "<PROJECT NAME>"
  }
}
```

This generates one PlantUML diagram file in the `./docs/` folder.
It shows the class UML diagram for the entire Language Pack.
The file can be included in further `.rst` files in the `./docs/` folder.

### Postgres View All

```json
{
  "generate": {
    "base_vocabgen": {
      "config": "ns:hmd-tmpl-vocabgen@<VERSION>:build:src/mickey/templates/postgres_view_all_config.json"
    }
  },
  "global_variables": {
    "base_package": "<PROJECT NAME>",
    "package_name": "<PROJECT NAME>"
  }
}
```

This generates one SQL file containing statements to create views for each entity.
The SQL can be used in conjunction with the standard entity database tables for a NeuronSphere MicroService.
It will convert the JSON column in the table for each entity into a view with a column per attribute.

### Postgres Views

```json
{
  "generate": {
    "base_vocabgen": {
      "config": "ns:hmd-tmpl-vocabgen@<VERSION>:build:src/mickey/templates/base_config.json"
    }
  },
  "global_variables": {
    "base_package": "<PROJECT NAME>",
    "package_name": "<PROJECT NAME>"
  }
}
```

This generates the same SQL as Postgres View All, but puts the view for each entity in a separate file.

### OPA Bundles

```json
{
  "generate": {
    "base_vocabgen": {
      "config": "ns:hmd-tmpl-vocabgen@<VERSION>:build:src/mickey/templates/base_config.json"
    }
  },
  "global_variables": {
    "base_package": "<PROJECT NAME>",
    "package_name": "<PROJECT NAME>"
  }
}
```

This generates one Rego policy document Jinja template.
The policy can be copied into the NeuronSphere MicroService project using this Language Pack to provide policies for the basic CRUD operations for each entity.
