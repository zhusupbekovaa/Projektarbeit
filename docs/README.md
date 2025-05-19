# Provide Data

## Create your first data asset

**[Management API](https://app.swaggerhub.com/apis/eclipse-edc-bot/management-api)**


### Step 1: Create the data

Create the data in the backend system of data-provider

```bash
$uri = 'http://dataprovider-submodelserver.tx.test/urn:uuid:b77c6d51-cd1f-4c9d-b5d4-091b22dd306b'

$headers = @{
    'Content-Type' = 'application/json'
}

$body = @{
    parentParts = @(
        @{
            validityPeriod = @{
                validFrom = "2023-03-21T08:47:14.438+01:00"
                validTo   = "2024-08-02T09:00:00.000+01:00"
            }
            parentCatenaXId = "urn:uuid:0733946c-59c6-41ae-9570-cb43a6e4c79e"
            quantity = @{
                quantityNumber  = 2.5
                measurementUnit = "unit:litre"
            }
            createdOn      = "2022-02-03T14:48:54.709Z"
            lastModifiedOn = "2022-02-03T14:48:54.709Z"
        }
    )
    catenaXId = "urn:uuid:2c57b0e9-a653-411d-bdcd-64787e9fd3a7"
} | ConvertTo-Json -Depth 10

$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $body

$response

```

Then verify that the data was created successfully.

```bash
$uri = 'http://dataprovider-submodelserver.tx.test/urn:uuid:b77c6d51-cd1f-4c9d-b5d4-091b22dd306b'
$headers = @{
    'Content-Type' = 'application/json'
}
$response = Invoke-RestMethod -Uri $uri -Method Get -Headers $headers
$response | ConvertTo-Json -Depth 10
```

#### Expected output
```json
{
    "parentParts":  [
                        {
                            "createdOn":  "2022-02-03T14:48:54.709Z",
                            "validityPeriod":  {
                                                   "validFrom":  "2023-03-21T08:47:14.438+01:00",
                                                   "validTo":  "2024-08-02T09:00:00.000+01:00"
                                               },
                            "parentCatenaXId":  "urn:uuid:0733946c-59c6-41ae-9570-cb43a6e4c79e",
                            "lastModifiedOn":  "2022-02-03T14:48:54.709Z",
                            "quantity":  {
                                             "measurementUnit":  "unit:litre",
                                             "quantityNumber":  2.5
                                         }
                        }
                    ],
    "catenaXId":  "urn:uuid:2c57b0e9-a653-411d-bdcd-64787e9fd3a7"
}
```

### Step 2: Create the asset

Data consumer, wants to consume data from data provider and data provider needs to create an asset for consumer.

```bash
# Define the URI
$uri = 'http://dataprovider-controlplane.tx.test/management/v3/assets'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST2'
}

# Define the body as a hashtable
$body = @{
    '@context' = @{
        '@vocab'    = 'https://w3id.org/edc/v0.0.1/ns/'
        'edc'       = 'https://w3id.org/edc/v0.0.1/ns/'
        'tx'        = 'https://w3id.org/tractusx/v0.0.1/ns/'
        'tx-auth'   = 'https://w3id.org/tractusx/auth/'
        'cx-policy' = 'https://w3id.org/catenax/policy/'
        'odrl'      = 'http://www.w3.org/ns/odrl/2/'
    }
    '@id' = '200'
    properties = @{
        description = 'Product EDC Demo Asset'
    }
    dataAddress = @{
        '@type'          = 'DataAddress'
        type             = 'HttpData'
        proxyPath        = 'true'
        proxyMethod      = 'true'
        proxyQueryParams = 'true'
        proxyBody        = 'true'
        baseUrl          = 'http://dataprovider-submodelserver.tx.test'
    }
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10
```

#### Expected output
```json
{
    "@type":  "IdResponse",
    "@id":  "200",
    "createdAt":  1747639371765,
    "@context":  {
                     "tx":  "https://w3id.org/tractusx/v0.0.1/ns/",
                     "tx-auth":  "https://w3id.org/tractusx/auth/",
                     "cx-policy":  "https://w3id.org/catenax/policy/",
                     "@vocab":  "https://w3id.org/edc/v0.0.1/ns/",
                     "edc":  "https://w3id.org/edc/v0.0.1/ns/",
                     "odrl":  "http://www.w3.org/ns/odrl/2/"
                 }
}
```

### Step 3: Validate the asset
Check that the asset was created successfully by running the following for requesting the specific asset with ID 200:
```bash
$uri = 'http://dataprovider-controlplane.tx.test/management/v3/assets/200'
$headers = @{
    'X-Api-Key' = 'TEST2'
}

$response = Invoke-RestMethod -Uri $uri -Method Get -Headers $headers

# Output the response
$response | ConvertTo-Json -Depth 10
```
or
```bash
$uri = 'http://dataprovider-controlplane.tx.test/management/v3/assets/request'
$headers = @{
    'x-api-key'    = 'TEST2'
    'Content-Type' = 'application/json'
}

# Define the body of the POST request
$body = @{
    # Add the necessary key-value pairs as required by the API
    # For example:
    # "parameter1" = "value1"
    # "parameter2" = "value2"
} | ConvertTo-Json -Depth 10

$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $body

# Output the response
$response | ConvertTo-Json -Depth 10
```

#### Expected output
The result for the first command shows just the newly created asset:
```json
{
    "@id":  "200",
    "@type":  "Asset",
    "properties":  {
                       "description":  "Product EDC Demo Asset",
                       "id":  "200"
                   },
    "dataAddress":  {
                        "@type":  "DataAddress",
                        "proxyPath":  "true",
                        "type":  "HttpData",
                        "proxyMethod":  "true",
                        "proxyQueryParams":  "true",
                        "proxyBody":  "true",
                        "baseUrl":  "http://dataprovider-submodelserver.tx.test"
                    },
    "@context":  {
                     "tx":  "https://w3id.org/tractusx/v0.0.1/ns/",
                     "tx-auth":  "https://w3id.org/tractusx/auth/",
                     "cx-policy":  "https://w3id.org/catenax/policy/",
                     "@vocab":  "https://w3id.org/edc/v0.0.1/ns/",
                     "edc":  "https://w3id.org/edc/v0.0.1/ns/",
                     "odrl":  "http://www.w3.org/ns/odrl/2/"
                 }
}
```

## Request the catalog

### Step 1: Request contract offers
Consumer sends a `CatalogRequest` to provider's connector to discover available assets:
```bash
# Define the URI
$uri = 'http://dataconsumer-1-controlplane.tx.test/management/v3/catalog/request'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST1'
}

# Define the body as a hashtable
$body = @{
    '@context' = @{
        '@vocab' = 'https://w3id.org/edc/v0.0.1/ns/'
    }
    '@type' = 'CatalogRequest'
    counterPartyAddress = 'http://dataprovider-controlplane.tx.test/api/v1/dsp'
    counterPartyId      = 'BPNL00000003AYRE'
    protocol            = 'dataspace-protocol-http'
    querySpec = @{
        offset = 0
        limit  = 50
    }
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10

```

#### Expected Output
Consumers's catalog response will include available assets like this:
```json
{
  "@id": "be17c3e7-3156-46db-8934-f1ea92d1f2a3",
  "@type": "dcat:Catalog",
  "dcat:dataset": [
    {
      "@id": "registry-asset",
      "@type": "dcat:Dataset",
      "odrl:hasPolicy": {
        "@id": "MTcxODBlMTAtNmFjNS00NTYzLWE2MTUtNGM1MjQ5ZTUxMTU5:cmVnaXN0cnktYXNzZXQ=:NzE0ODk2YjMtY2VlYy00NmY5LWE5ZTgtY2NiMWI1NGUzOTgy",
        "@type": "odrl:Set",
        "odrl:permission": {
          "odrl:target": "registry-asset",
          "odrl:action": {
            "odrl:type": "USE"
          },
          "odrl:constraint": {
            "odrl:or": {
              "odrl:leftOperand": "PURPOSE",
              "odrl:operator": {
                "@id": "odrl:eq"
              },
              "odrl:rightOperand": "ID 3.0 Trace"
            }
          }
        },
        "odrl:prohibition": [],
        "odrl:obligation": [],
        "odrl:target": "registry-asset"
      },
      "dcat:distribution": [
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "HttpProxy"
          },
          "dcat:accessService": "49a693e0-835d-457a-99b4-e781f2bd643d"
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "AmazonS3"
          },
          "dcat:accessService": "49a693e0-835d-457a-99b4-e781f2bd643d"
        }
      ],
      "edc:type": "data.core.digitalTwinRegistry",
      "edc:description": "Digital Twin Registry Endpoint of IRS DEV",
      "edc:id": "registry-asset"
    },
    {
      "@id": "urn:uuid:dc641d45-95e7-4284-a472-43f30255d0cb",
      "@type": "dcat:Dataset",
      "odrl:hasPolicy": {
        "@id": "MTIzYjJlM2QtNTUxOC00ZWViLWFmMGItNTU5ZTYxZGY3Zjhk:dXJuOnV1aWQ6ZGM2NDFkNDUtOTVlNy00Mjg0LWE0NzItNDNmMzAyNTVkMGNi:YzBhOGFhOTQtNzg4OS00Y2MxLTkzNmMtMWYwMTNkODc3Nzk4",
        "@type": "odrl:Set",
        "odrl:permission": {
          "odrl:target": "urn:uuid:dc641d45-95e7-4284-a472-43f30255d0cb",
          "odrl:action": {
            "odrl:type": "USE"
          },
          "odrl:constraint": {
            "odrl:or": {
              "odrl:leftOperand": "PURPOSE",
              "odrl:operator": {
                "@id": "odrl:eq"
              },
              "odrl:rightOperand": "ID 3.0 Trace"
            }
          }
        },
        "odrl:prohibition": [],
        "odrl:obligation": [],
        "odrl:target": "urn:uuid:dc641d45-95e7-4284-a472-43f30255d0cb"
      },
      "dcat:distribution": [
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "HttpProxy"
          },
          "dcat:accessService": "49a693e0-835d-457a-99b4-e781f2bd643d"
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "AmazonS3"
          },
          "dcat:accessService": "49a693e0-835d-457a-99b4-e781f2bd643d"
        }
      ],
      "edc:description": "IRS EDC Test Asset",
      "edc:id": "urn:uuid:dc641d45-95e7-4284-a472-43f30255d0cb"
    }
  ],
  "dcat:service": {
    "@id": "49a693e0-835d-457a-99b4-e781f2bd643d",
    "@type": "dcat:DataService",
    "dct:terms": "connector",
    "dct:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
  },
  "edc:participantId": "BPNL00000003AYRE",
  "@context": {
    "dct": "http://purl.org/dc/terms/",
    "tx": "https://w3id.org/tractusx/v0.0.1/ns/",
    "edc": "https://w3id.org/edc/v0.0.1/ns/",
    "dcat": "https://www.w3.org/ns/dcat/",
    "odrl": "http://www.w3.org/ns/odrl/2/",
    "dspace": "https://w3id.org/dspace/v0.8/"
  }
}
```

## Create a first access policy
Provier creates an **Access Policy** to determine who can view the data offer.
### Step 1: Create the policy
```bash
# Define the URI
$uri = 'http://dataprovider-controlplane.tx.test/management/v3/policydefinitions'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST2'
}

# Define the body as a hashtable
$body = @{
    '@context' = @{
        'odrl' = 'http://www.w3.org/ns/odrl/2/'
    }
    '@type' = 'PolicyDefinitionRequestDto'
    '@id'   = '200'
    policy  = @{
        '@type'          = 'odrl:Set'
        'odrl:permission' = @(
            @{
                'odrl:action'    = 'USE'
                'odrl:constraint' = @{
                    '@type' = 'LogicalConstraint'
                    'odrl:or' = @(
                        @{
                            '@type'           = 'Constraint'
                            'odrl:leftOperand' = @{
                                '@id' = 'BusinessPartnerNumber'
                            }
                            'odrl:operator' = @{
                                '@id' = 'odrl:eq'
                            }
                            'odrl:rightOperand' = 'BPNL00000003AZQP'
                        }
                    )
                }
            }
        )
    }
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10

```
In this case a **Access Policy** is created. The assigned asset will only be visible as a data offer for the Business Partner Number `BPNL00000003AZQP`.

#### Expected Output 
The policy was successfully created, if the response is something like this
```json
{
    "@type":  "IdResponse",
    "@id":  "200",
    "createdAt":  1747639778551,
    "@context":  {
                     "tx":  "https://w3id.org/tractusx/v0.0.1/ns/",
                     "tx-auth":  "https://w3id.org/tractusx/auth/",
                     "cx-policy":  "https://w3id.org/catenax/policy/",
                     "@vocab":  "https://w3id.org/edc/v0.0.1/ns/",
                     "edc":  "https://w3id.org/edc/v0.0.1/ns/",
                     "odrl":  "http://www.w3.org/ns/odrl/2/"
                 }
}
```

### Step 2: Request catalog - second try
Now that Provider has created an access policy, consumer can once again try to access providers's offer by running this:

```bash
# Define the URI
$uri = 'http://dataconsumer-1-controlplane.tx.test/management/v3/catalog/request'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST1'
}

# Define the body as a hashtable
$body = @{
    '@context' = @{
        '@vocab' = 'https://w3id.org/edc/v0.0.1/ns/'
    }
    '@type'              = 'CatalogRequest'
    counterPartyAddress  = 'http://dataprovider-controlplane.tx.test/api/v1/dsp'
    counterPartyId       = 'BPNL00000003AYRE'
    protocol             = 'dataspace-protocol-http'
    querySpec = @{
        offset = 0
        limit  = 50
    }
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10

```


#### Expected Output
```json
{
  "@id": "03500817-60c1-4fca-81b2-0dd2e81651aa",
  "@type": "dcat:Catalog",
  "dcat:dataset": [
    {
      "@id": "registry-asset",
      "@type": "dcat:Dataset",
      "odrl:hasPolicy": {
        "@id": "Yzc1MDVjNmUtZDk4MC00NjFiLTg4NTktNjlhZWFmYzU1MjQx:cmVnaXN0cnktYXNzZXQ=:MmU3OTY3YWItMjU3MS00Nzc5LWEzYzctYTQwMzc3NDM1MTFl",
        "@type": "odrl:Offer",
        "odrl:permission": {
          "odrl:action": {
            "@id": "odrl:use"
          },
          "odrl:constraint": {
            "odrl:or": {
              "odrl:leftOperand": {
                "@id": "cx-policy:Membership"
              },
              "odrl:operator": {
                "@id": "odrl:eq"
              },
              "odrl:rightOperand": "active"
            }
          }
        },
        "odrl:prohibition": [],
        "odrl:obligation": []
      },
      "dcat:distribution": [
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "AzureStorage-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "HttpData-PULL"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "HttpData-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "AmazonS3-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        }
      ],
      "type": "data.core.digitalTwinRegistry",
      "dct:type": {
        "@id": "https://w3id.org/catenax/taxonomy#DigitalTwinRegistry"
      },
      "description": "Digital Twin Registry Endpoint",
      "https://w3id.org/catenax/ontology/common#version": "3.0",
      "id": "registry-asset"
    },
    {
      "@id": "urn:uuid:d535e16e-b3af-47f7-8353-0d71ef12d5c0",
      "@type": "dcat:Dataset",
      "odrl:hasPolicy": {
        "@id": "YzQ1NzFkMWQtYTYxMy00ZTNmLTkzNjMtODVhNDJkMzYyNzVj:dXJuOnV1aWQ6ZDUzNWUxNmUtYjNhZi00N2Y3LTgzNTMtMGQ3MWVmMTJkNWMw:ODdkMDcwNjktODRmOC00OTcwLThhMDctY2E2ZmU3OWQxODcy",
        "@type": "odrl:Offer",
        "odrl:permission": {
          "odrl:action": {
            "@id": "odrl:use"
          },
          "odrl:constraint": {
            "odrl:or": {
              "odrl:leftOperand": {
                "@id": "cx-policy:Membership"
              },
              "odrl:operator": {
                "@id": "odrl:eq"
              },
              "odrl:rightOperand": "active"
            }
          }
        },
        "odrl:prohibition": [],
        "odrl:obligation": []
      },
      "dcat:distribution": [
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "AzureStorage-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "HttpData-PULL"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "HttpData-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "AmazonS3-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        }
      ],
      "description": "Umbrella EDC Test Asset",
      "id": "urn:uuid:d535e16e-b3af-47f7-8353-0d71ef12d5c0"
    },
    {
      "@id": "urn:uuid:a74c44a1-6fd6-4b56-a498-47e3ac391333",
      "@type": "dcat:Dataset",
      "odrl:hasPolicy": {
        "@id": "NTBmN2EzZDAtNDQwMS00MmQxLThmZGItMTJlM2ZiNDk5OWNm:dXJuOnV1aWQ6YTc0YzQ0YTEtNmZkNi00YjU2LWE0OTgtNDdlM2FjMzkxMzMz:ZDk1NGMyOGMtMGY1MC00N2E0LWFlYjUtMTdkMWViMmNiNWI4",
        "@type": "odrl:Offer",
        "odrl:permission": {
          "odrl:action": {
            "@id": "odrl:use"
          },
          "odrl:constraint": {
            "odrl:or": {
              "odrl:leftOperand": {
                "@id": "cx-policy:Membership"
              },
              "odrl:operator": {
                "@id": "odrl:eq"
              },
              "odrl:rightOperand": "active"
            }
          }
        },
        "odrl:prohibition": [],
        "odrl:obligation": []
      },
      "dcat:distribution": [
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "AzureStorage-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "HttpData-PULL"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "HttpData-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        },
        {
          "@type": "dcat:Distribution",
          "dct:format": {
            "@id": "AmazonS3-PUSH"
          },
          "dcat:accessService": {
            "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
            "@type": "dcat:DataService",
            "dcat:endpointDescription": "dspace:connector",
            "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
            "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
          }
        }
      ],
      "description": "Umbrella EDC Test Asset",
      "id": "urn:uuid:a74c44a1-6fd6-4b56-a498-47e3ac391333"
    }
  ],
  "dcat:catalog": [],
  "dcat:distribution": [],
  "dcat:service": {
    "@id": "69bc0663-5b91-408c-accb-ab813e723e8a",
    "@type": "dcat:DataService",
    "dcat:endpointDescription": "dspace:connector",
    "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
    "dcat:endpointURL": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
  },
  "dspace:participantId": "BPNL00000003AYRE",
  "@context": {
    "tx": "https://w3id.org/tractusx/v0.0.1/ns/",
    "tx-auth": "https://w3id.org/tractusx/auth/",
    "cx-policy": "https://w3id.org/catenax/policy/",
    "dcat": "http://www.w3.org/ns/dcat#",
    "dct": "http://purl.org/dc/terms/",
    "odrl": "http://www.w3.org/ns/odrl/2/",
    "dspace": "https://w3id.org/dspace/v0.8/",
    "@vocab": "https://w3id.org/edc/v0.0.1/ns/",
    "edc": "https://w3id.org/edc/v0.0.1/ns/"
  }
}
```

## Define a contract definition
Consumer still cannot see the asset because Provider hasn't created a **Contract Definition**. Contract definitions link assets and policies to define usage terms.

### Step 1: Create the contract definition
First of all run this command to check if the policy is created correctly:
```bash
# Define the URI
$uri = 'http://dataprovider-controlplane.tx.test/management/v3/policydefinitions/200'

# Define the headers
$headers = @{
    'X-Api-Key' = 'TEST2'
}

# Send the GET request
$response = Invoke-RestMethod -Uri $uri -Method Get -Headers $headers

# Output the response
$response | ConvertTo-Json -Depth 10

```

The policy with ID: 200 was successfully created, if the response is something like this:
```json
{
  "@id": "200",
  "@type": "PolicyDefinition",
  "createdAt": 1747639778551,
  "policy": {
    "@id": "db890b28-19e8-4c34-8f0d-34c2d480acc1",
    "@type": "odrl:Set",
    "odrl:permission": {
      "odrl:action": {
        "@id": "USE"
      },
      "odrl:constraint": {
        "odrl:or": {
          "odrl:leftOperand": {
            "@id": "BusinessPartnerNumber"
          },
          "odrl:operator": {
            "@id": "odrl:eq"
          },
          "odrl:rightOperand": "BPNL00000003AZQP"
        }
      }
    },
    "odrl:prohibition": [],
    "odrl:obligation": []
  },
  "@context": {
    "tx": "https://w3id.org/tractusx/v0.0.1/ns/",
    "tx-auth": "https://w3id.org/tractusx/auth/",
    "cx-policy": "https://w3id.org/catenax/policy/",
    "@vocab": "https://w3id.org/edc/v0.0.1/ns/",
    "edc": "https://w3id.org/edc/v0.0.1/ns/",
    "odrl": "http://www.w3.org/ns/odrl/2/"
  }
}
```

Run this command to create a contract definition including the asset and the policies you have created:

```bash
# Define the URI
$uri = 'http://dataprovider-controlplane.tx.test/management/v3/contractdefinitions'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST2'
}

# Define the body as a hashtable
$body = @{
    '@context' = @{}
    '@id'      = '200'
    '@type'    = 'ContractDefinition'
    accessPolicyId   = '200'
    contractPolicyId = '200'
    assetsSelector = @{
        '@type' = 'CriterionDto'
        operandLeft  = 'https://w3id.org/edc/v0.0.1/ns/id'
        operator     = '='
        operandRight = '200'
    }
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10

```

#### Expected Output
```json
{
    "@type":  "IdResponse",
    "@id":  "200",
    "createdAt":  1747640237259,
    "@context":  {
                     "tx":  "https://w3id.org/tractusx/v0.0.1/ns/",
                     "tx-auth":  "https://w3id.org/tractusx/auth/",
                     "cx-policy":  "https://w3id.org/catenax/policy/",
                     "@vocab":  "https://w3id.org/edc/v0.0.1/ns/",
                     "edc":  "https://w3id.org/edc/v0.0.1/ns/",
                     "odrl":  "http://www.w3.org/ns/odrl/2/"
                 }
}
```

## Verify data availability

Consumer re-requests the catalog. This time, she should see Bob's asset in the catalog:
```bash
# Define the URI
$uri = 'http://dataconsumer-1-controlplane.tx.test/management/v3/catalog/request'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST1'
}

# Define the body as a hashtable
$body = @{
    '@context' = @{
        '@vocab' = 'https://w3id.org/edc/v0.0.1/ns/'
    }
    '@type'             = 'CatalogRequest'
    counterPartyAddress = 'http://dataprovider-controlplane.tx.test/api/v1/dsp'
    counterPartyId      = 'BPNL00000003AYRE'
    protocol            = 'dataspace-protocol-http'
    querySpec = @{
        filterExpression = @(
            @{
                operandLeft  = 'https://w3id.org/edc/v0.0.1/ns/id'
                operator     = '='
                operandRight = '200'
            }
        )
    }
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10

```

### Expected Output
```json
{
  "@id": "ad6b2272-a131-4d75-9efd-0c7529d1109f",
  "@type": "dcat:Catalog",
  "dspace:participantId": "BPNL00000003AYRE",
  "dcat:dataset": {
    "@id": "200",
    "@type": "dcat:Dataset",
    "odrl:hasPolicy": {
      "@id": "MjAw:MjAw:YzNlOWVjYzEtMDcwNS00YWI1LThhM2UtOWNmYTA5NTg2ZWU4",
      "@type": "odrl:Offer",
      "odrl:permission": {
        "odrl:action": {
          "odrl:type": "USE"
        },
        "odrl:constraint": {
          "odrl:or": {
            "odrl:leftOperand": "BusinessPartnerNumber",
            "odrl:operator": {
              "@id": "odrl:eq"
            },
            "odrl:rightOperand": "BPNL00000003AZQP"
          }
        }
      },
      "odrl:prohibition": [],
      "odrl:obligation": []
    },
    "dcat:distribution": [
      {
        "@type": "dcat:Distribution",
        "dct:format": {
          "@id": "AzureStorage-PUSH"
        },
        "dcat:accessService": {
          "@id": "ed228dd5-d1e6-45be-84be-20ba27829e50",
          "@type": "dcat:DataService",
          "dcat:endpointDescription": "dspace:connector",
          "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
          "dct:terms": "dspace:connector",
          "dct:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
        }
      },
      {
        "@type": "dcat:Distribution",
        "dct:format": {
          "@id": "HttpData-PULL"
        },
        "dcat:accessService": {
          "@id": "ed228dd5-d1e6-45be-84be-20ba27829e50",
          "@type": "dcat:DataService",
          "dcat:endpointDescription": "dspace:connector",
          "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
          "dct:terms": "dspace:connector",
          "dct:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
        }
      },
      {
        "@type": "dcat:Distribution",
        "dct:format": {
          "@id": "HttpData-PUSH"
        },
        "dcat:accessService": {
          "@id": "ed228dd5-d1e6-45be-84be-20ba27829e50",
          "@type": "dcat:DataService",
          "dcat:endpointDescription": "dspace:connector",
          "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
          "dct:terms": "dspace:connector",
          "dct:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
        }
      },
      {
        "@type": "dcat:Distribution",
        "dct:format": {
          "@id": "AmazonS3-PUSH"
        },
        "dcat:accessService": {
          "@id": "ed228dd5-d1e6-45be-84be-20ba27829e50",
          "@type": "dcat:DataService",
          "dcat:endpointDescription": "dspace:connector",
          "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
          "dct:terms": "dspace:connector",
          "dct:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
        }
      }
    ],
    "description": "Product EDC Demo Asset",
    "id": "200"
  },
  "dcat:service": {
    "@id": "ed228dd5-d1e6-45be-84be-20ba27829e50",
    "@type": "dcat:DataService",
    "dcat:endpointDescription": "dspace:connector",
    "dcat:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp",
    "dct:terms": "dspace:connector",
    "dct:endpointUrl": "http://dataprovider-controlplane.tx.test/api/v1/dsp"
  },
  "participantId": "BPNL00000003AYRE",
  "@context": {
    "@vocab": "https://w3id.org/edc/v0.0.1/ns/",
    "edc": "https://w3id.org/edc/v0.0.1/ns/",
    "tx": "https://w3id.org/tractusx/v0.0.1/ns/",
    "tx-auth": "https://w3id.org/tractusx/auth/",
    "cx-policy": "https://w3id.org/catenax/policy/",
    "dcat": "http://www.w3.org/ns/dcat#",
    "dct": "http://purl.org/dc/terms/",
    "odrl": "http://www.w3.org/ns/odrl/2/",
    "dspace": "https://w3id.org/dspace/v0.8/"
  }
}
```

Finally Consumer can see the Contract Offer from provider.

# Consume Data

## Step 1: Request the catalog
To see provider's data offerings, consumer must request access to his catalog. The catalog shows all the assets that consumer can consume from provider.

### Catalog request (consumer)
```bash
# Define the URI
$uri = 'http://dataconsumer-1-controlplane.tx.test/management/v3/catalog/request'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST1'
}

# Define the body as a hashtable
$body = @{
    '@context' = @{
        '@vocab' = 'https://w3id.org/edc/v0.0.1/ns/'
    }
    '@type'             = 'CatalogRequest'
    counterPartyAddress = 'http://dataprovider-controlplane.tx.test/api/v1/dsp'
    counterPartyId      = 'BPNL00000003AYRE'
    protocol            = 'dataspace-protocol-http'
    querySpec = @{
        filterExpression = @{
            operandLeft  = 'https://w3id.org/edc/v0.0.1/ns/id'
            operator     = '='
            operandRight = '200'
        }
        offset = 0
        limit  = 50
    }
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10
```

### Catalog response
The response (here just an extract) will contain the `@id` `200` of the asset that consumer wants to consume, but also the `@id` `MjAw:MjAw:NjRhYzJiZjAtNTVjMi00OWIzLTlhM2MtYTNmMjFjNWIwZTEz` of the offer that consumer needs to reference in the negotiation.
```json 
"{\"@id\":\"09a65522-9f70-45ee-9354-7171fb1e996a\",\"@type\":\"dcat:Catalog\",\"dcat:dataset\":{\"@id\":\"200\",\"@type\":\"dcat:Dataset\",\"odrl:hasPolicy\":{\"@id\":\"MjAw:MjAw:NjRhYzJiZjAtNTVjMi00OWIzLTlhM2MtYTNmMjFjNWIwZTEz\",\"@type\":\"odrl:Offer\",\"odrl:permission\":{\"odrl:action\":{\"@id\":\"USE\"},\"odrl:constraint\":{\"odrl:or\":{\"odrl:leftOperand\":{\"@id\":\"BusinessPartnerNumber\"},\"odrl:operator\":{\"@id\":\"odrl:eq\"},\"odrl:rightOperand\":\"BPNL00000003AZQP\"}}},\"odrl:prohibition\":[],\"odrl:obligation\":[]},\"dcat:distribution\":[{\"@type\":\"dcat:Distribution\",\"dct:format\":{\"@id\":\"AzureStorage-PUSH\"},\"dcat:accessService\":{\"@id\":\"69bc0663-5b91-408c-accb-ab813e723e8a\",\"@type\":\"dcat:DataService\",\"dcat:endpointDescription\":\"dspace:connector\",\"dcat:endpointUrl\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\",\"dcat:endpointURL\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\"}},{\"@type\":\"dcat:Distribution\",\"dct:format\":{\"@id\":\"HttpData-PULL\"},\"dcat:accessService\":{\"@id\":\"69bc0663-5b91-408c-accb-ab813e723e8a\",\"@type\":\"dcat:DataService\",\"dcat:endpointDescription\":\"dspace:connector\",\"dcat:endpointUrl\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\",\"dcat:endpointURL\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\"}},{\"@type\":\"dcat:Distribution\",\"dct:format\":{\"@id\":\"HttpData-PUSH\"},\"dcat:accessService\":{\"@id\":\"69bc0663-5b91-408c-accb-ab813e723e8a\",\"@type\":\"dcat:DataService\",\"dcat:endpointDescription\":\"dspace:connector\",\"dcat:endpointUrl\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\",\"dcat:endpointURL\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\"}},{\"@type\":\"dcat:Distribution\",\"dct:format\":{\"@id\":\"AmazonS3-PUSH\"},\"dcat:accessService\":{\"@id\":\"69bc0663-5b91-408c-accb-ab813e723e8a\",\"@type\":\"dcat:DataService\",\"dcat:endpointDescription\":\"dspace:connector\",\"dcat:endpointUrl\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\",\"dcat:endpointURL\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\"}}],\"description\":\"Product EDC Demo Asset\",\"id\":\"200\"},\"dcat:catalog\":[],\"dcat:distribution\":[],\"dcat:service\":{\"@id\":\"69bc0663-5b91-408c-accb-ab813e723e8a\",\"@type\":\"dcat:DataService\",\"dcat:endpointDescription\":\"dspace:connector\",\"dcat:endpointUrl\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\",\"dcat:endpointURL\":\"http://dataprovider-controlplane.tx.test/api/v1/dsp\"},\"dspace:participantId\":\"BPNL00000003AYRE\",\"@context\":{\"tx\":\"https://w3id.org/tractusx/v0.0.1/ns/\",\"tx-auth\":\"https://w3id.org/tractusx/auth/\",\"cx-policy\":\"https://w3id.org/catenax/policy/\",\"dcat\":\"http://www.w3.org/ns/dcat#\",\"dct\":\"http://purl.org/dc/terms/\",\"odrl\":\"http://www.w3.org/ns/odrl/2/\",\"dspace\":\"https://w3id.org/dspace/v0.8/\",\"@vocab\":\"https://w3id.org/edc/v0.0.1/ns/\",\"edc\":\"https://w3id.org/edc/v0.0.1/ns/\"}}"
```

## Step 2: Initiate an edr
To consume the data, consumer uses the `OFFER_ID` `MjAw:MjAw:NjRhYzJiZjAtNTVjMi00OWIzLTlhM2MtYTNmMjFjNWIwZTEz` from the previous catalog response and the target `ASSET_ID` `200` to initiate an [EDR](https://github.com/eclipse-tractusx/tractusx-edc/blob/main/docs/usage/management-api-walkthrough/07_edrs.md).

### EDR request
```bash
# Define the URI
$uri = 'http://dataconsumer-1-controlplane.tx.test/management/v3/edrs'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST1'
}

# Define the body as a hashtable
$body = @{
    '@context' = @(
        'https://w3id.org/tractusx/policy/v1.0.0',
        'http://www.w3.org/ns/odrl.jsonld',
        @{ '@vocab' = 'https://w3id.org/edc/v0.0.1/ns/' }
    )
    '@type'             = 'ContractRequest'
    counterPartyAddress = 'http://dataprovider-controlplane.tx.test/api/v1/dsp'
    protocol            = 'dataspace-protocol-http'
    policy = @{
        assigner = 'BPNL00000003AYRE'
        target   = '200'
        '@id'    = 'MjAw:MjAw:NjRhYzJiZjAtNTVjMi00OWIzLTlhM2MtYTNmMjFjNWIwZTEz'
        '@type'  = 'odrl:Offer'
        'odrl:permission' = @{
            'odrl:action' = @{
                'odrl:type' = 'USE'
            }
            'odrl:constraint' = @{
                'odrl:or' = @{
                    'odrl:leftOperand'  = 'BusinessPartnerNumber'
                    'odrl:operator'     = @{ '@id' = 'odrl:eq' }
                    'odrl:rightOperand' = 'BPNL00000003AZQP'
                }
            }
        }
        'odrl:prohibition' = @()
        'odrl:obligation'  = @()
    }
    callbackAddresses = @()
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10

```

### EDR response
The response also includes the `@id` `6602e7ae-aabc-4d47-80cc-111cd762ff64` of the EDR, which consumer can use to get the EDR and more information.
```json
{
    "@type":  "IdResponse",
    "@id":  "6602e7ae-aabc-4d47-80cc-111cd762ff64",
    "createdAt":  1747640869402,
    "@context":  {
                     "tx":  "https://w3id.org/tractusx/v0.0.1/ns/",
                     "tx-auth":  "https://w3id.org/tractusx/auth/",
                     "cx-policy":  "https://w3id.org/catenax/policy/",
                     "@vocab":  "https://w3id.org/edc/v0.0.1/ns/",
                     "edc":  "https://w3id.org/edc/v0.0.1/ns/",
                     "odrl":  "http://www.w3.org/ns/odrl/2/"
                 }
}
```

## Step 3: Query cached edrs
Consumer now queries cached EDRs using the {{CONTRACT_NEGOTIATION_ID}} `6602e7ae-aabc-4d47-80cc-111cd762ff64` from the previous response.

### Query edrs request
```bash
# Define the URI
$uri = 'http://dataconsumer-1-controlplane.tx.test/management/v3/edrs/request'

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST1'
}

# Define the body as a hashtable
$body = @{
    '@context' = @{
        '@vocab' = 'https://w3id.org/edc/v0.0.1/ns/'
    }
    '@type' = 'QuerySpec'
    filterExpression = @(
        @{
            operandLeft  = 'contractNegotiationId'
            operator     = '='
            operandRight = '6602e7ae-aabc-4d47-80cc-111cd762ff64'
        }
    )
}

# Convert the body to JSON
$jsonBody = $body | ConvertTo-Json -Depth 10

# Send the POST request
$response = Invoke-RestMethod -Uri $uri -Method Post -Headers $headers -Body $jsonBody

# Output the response
$response | ConvertTo-Json -Depth 10

```

### Query edrs response
The response will contain the EDR with the `@id` `ad1d8447-0c62-480a-94d9-e6b510ccce35` and the `transferProcessId` `ad1d8447-0c62-480a-94d9-e6b510ccce35`, which is important for the next step. of the contract agreement.
```json
{
    "@id":  "ad1d8447-0c62-480a-94d9-e6b510ccce35",
    "@type":  "EndpointDataReferenceEntry",
    "providerId":  "BPNL00000003AYRE",
    "assetId":  "200",
    "agreementId":  "74ea11d8-8d46-4530-aa35-3a8ef13d6dcd",
    "transferProcessId":  "ad1d8447-0c62-480a-94d9-e6b510ccce35",
    "createdAt":  1747640872978,
    "contractNegotiationId":  "6602e7ae-aabc-4d47-80cc-111cd762ff64",
    "@context":  {
                     "tx":  "https://w3id.org/tractusx/v0.0.1/ns/",
                     "tx-auth":  "https://w3id.org/tractusx/auth/",
                     "cx-policy":  "https://w3id.org/catenax/policy/",
                     "@vocab":  "https://w3id.org/edc/v0.0.1/ns/",
                     "edc":  "https://w3id.org/edc/v0.0.1/ns/",
                     "odrl":  "http://www.w3.org/ns/odrl/2/"
                 }
}
```

## Step 4: Get authorization details
Consumer uses the {{TRANSFER_PROCESS_ID}} `ad1d8447-0c62-480a-94d9-e6b510ccce35` to get authorization details.

### Authorization request

```bash
# Define the Transfer Process ID
$transferProcessId = 'ad1d8447-0c62-480a-94d9-e6b510ccce35'  # Replace with the actual ID

# Construct the URI
$uri = "http://dataconsumer-1-controlplane.tx.test/management/v3/edrs/$transferProcessId/dataaddress?auto_refresh=false"

# Define the headers
$headers = @{
    'Content-Type' = 'application/json'
    'X-Api-Key'    = 'TEST1'
}

# Send the GET request
$response = Invoke-RestMethod -Uri $uri -Method Get -Headers $headers

# Output the response
$response | ConvertTo-Json -Depth 10
```

### Authorization response
The response will contain the authorization details, including the `endpoint` and `authorization`which are needed to fetch the data via the data plane.

```json
{
    "@type":  "DataAddress",
    "flowType":  "PULL",
    "endpointType":  "https://w3id.org/idsa/v4.1/HTTP",
    "tx-auth:refreshEndpoint":  "http://dataprovider-dataplane.tx.test/api/public/token",
    "transferTypeDestination":  "HttpData",
    "tx-auth:audience":  "did:web:ssi-dim-wallet-stub.tx.test:BPNL00000003AZQP",
    "type":  "https://w3id.org/idsa/v4.1/HTTP",
    "endpoint":  "http://dataprovider-dataplane.tx.test/api/public",
    "tx-auth:refreshToken":  "eyJraWQiOiJ0b2tlblNpZ25lclB1YmxpY0tleSIsImFsZyI6IlJTMjU2In0.eyJleHAiOjE3NDc2NDExNzIsImlhdCI6MTc0NzY0MDg3MiwianRpIjoiMGM3Nzk2YjMtMzljNS00Mzk2LWJlMmQtMDFiMjllNTE2NmJmIn0.XkYSx2htw3rysld8UHfVGRWMrYxoy-lOB04YaDap1tuAvWYv5B0qKOC9JYKpW8lgkE37sCHEGyKz3T9THo3sfAj7g6OQ64B4TTU-eZ_UzLJKvujr0teksw9LDnMBAZ7AFB8ewJNO7kyvtM8w-2YZmrzT6IBJcJ-MMbUyj6rDlrCsRlXs92XIy4ywZFKtOH02Nd4lFshEnV5wpScQsaVUo9S0z8ealgmAJoiJyoYTburr66OYXeDM5j2EvvQRznM7bm-DIJfT3eu4mbfpBpNIQVSEqfwvM5uFymUu_PHDkJYRSYzFqbP0DdRe-Lj6sUdn67KP-M4l3nFYtOxz016FpA",
    "tx-auth:expiresIn":  "300",
    "authorization":  "eyJraWQiOiJ0b2tlblNpZ25lclB1YmxpY0tleSIsImFsZyI6IlJTMjU2In0.eyJpc3MiOiJCUE5MMDAwMDAwMDNBWVJFIiwiYXVkIjoiQlBOTDAwMDAwMDAzQVpRUCIsInN1YiI6IkJQTkwwMDAwMDAwM0FZUkUiLCJleHAiOjE3NDc2NDExNzIsImlhdCI6MTc0NzY0MDg3MiwianRpIjoiMDYwZTc0YzgtZGE3ZS00ODM1LTk4NWMtYWEzMjJkY2M0MDZiIn0.UXWa2Lizvx4GCX4gSMSUJpGhZaAe-CFRMyaGPsDaLt3IC-GnP35w80oXwjLhwhyFzIcwe7MVbHBsWZOYe3kf99pM80VP43TAZP_Lu1PeeNnlBjDmDucf9BgPAUeoEWMO_vZK64BzTjnUNsTlOc1WEG8lZr-c50nqooufgRMvS-kUaa1FjTYQpJpg15dtes0oJ4_DnlfP1KeWb9rGsJH50rvU0V3DyQ0o93r3txVk_-Pb7bvOIPzCQvN3FfeB9N7Vqgq1dCq2ToswAm5QlfIy03HhzEzPOt2HsM9xMttv25hbhOTJzUU1uGdNXVmLmoPLg4xjZ_mlJ43pmcWDT72Oow",
    "tx-auth:refreshAudience":  "did:web:ssi-dim-wallet-stub.tx.test:BPNL00000003AZQP",
    "@context":  {
                     "tx":  "https://w3id.org/tractusx/v0.0.1/ns/",
                     "tx-auth":  "https://w3id.org/tractusx/auth/",
                     "cx-policy":  "https://w3id.org/catenax/policy/",
                     "@vocab":  "https://w3id.org/edc/v0.0.1/ns/",
                     "edc":  "https://w3id.org/edc/v0.0.1/ns/",
                     "odrl":  "http://www.w3.org/ns/odrl/2/"
                 }
}
```
## Step 5: Fetch data
Using the {{ENDPOINT}} and {{TOKEN}} from the response, Consumer fetches the data. In our example the `uuid` `urn:uuid:b77c6d51-cd1f-4c9d-b5d4-091b22dd306b` for the data is already known.

### Fetch data request
```bash
# Define the endpoint and token
$endpoint = 'http://dataprovider-dataplane.tx.test/api/public'  # Replace with your actual endpoint
$resourceId = 'urn:uuid:b77c6d51-cd1f-4c9d-b5d4-091b22dd306b'  # Replace with your actual resource ID
$token = 'eyJraWQiOiJ0b2tlblNpZ25lclB1YmxpY0tleSIsImFsZyI6IlJTMjU2In0.eyJpc3MiOiJCUE5MMDAwMDAwMDNBWVJFIiwiYXVkIjoiQlBOTDAwMDAwMDAzQVpRUCIsInN1YiI6IkJQTkwwMDAwMDAwM0FZUkUiLCJleHAiOjE3NDc2NDExNzIsImlhdCI6MTc0NzY0MDg3MiwianRpIjoiMDYwZTc0YzgtZGE3ZS00ODM1LTk4NWMtYWEzMjJkY2M0MDZiIn0.UXWa2Lizvx4GCX4gSMSUJpGhZaAe-CFRMyaGPsDaLt3IC-GnP35w80oXwjLhwhyFzIcwe7MVbHBsWZOYe3kf99pM80VP43TAZP_Lu1PeeNnlBjDmDucf9BgPAUeoEWMO_vZK64BzTjnUNsTlOc1WEG8lZr-c50nqooufgRMvS-kUaa1FjTYQpJpg15dtes0oJ4_DnlfP1KeWb9rGsJH50rvU0V3DyQ0o93r3txVk_-Pb7bvOIPzCQvN3FfeB9N7Vqgq1dCq2ToswAm5QlfIy03HhzEzPOt2HsM9xMttv25hbhOTJzUU1uGdNXVmLmoPLg4xjZ_mlJ43pmcWDT72Oow'

# Construct the full URI
$uri = "$endpoint/$resourceId"

# Set up the headers with the Authorization token
$headers = @{
    'Authorization' = "Bearer $token"
}

# Send the GET request
$response = Invoke-RestMethod -Uri $uri -Method Get -Headers $headers

# Convert the response to a JSON string with indentation
$response | ConvertTo-Json -Depth 10
```

### Fetch data response

```json
{
  "parentParts": [
    {
      "validityPeriod": {
        "validFrom": "2023-03-21T08:47:14.438+01:00",
        "validTo": "2024-08-02T09:00:00.000+01:00"
      },
      "parentCatenaXId": "urn:uuid:0733946c-59c6-41ae-9570-cb43a6e4c79e",
      "quantity": {
        "quantityNumber": 2.5,
        "measurementUnit": "unit:litre"
      },
      "createdOn": "2022-02-03T14:48:54.709Z",
      "lastModifiedOn": "2022-02-03T14:48:54.709Z"
    }
  ],
  "catenaXId": "urn:uuid:2c57b0e9-a653-411d-bdcd-64787e9fd3a7"
}
```
