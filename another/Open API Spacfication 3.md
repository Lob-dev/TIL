2020.12.11

### **Open API Specfication 3?**

OAS 는 RESTful API에 대한 정보들을 정의하여 소스 코드에 접근하지 않아도 서비스의 기능을 검색, 확인하고 사용할 수 있게끔 지원해주는 표준 인터페이스이다.

<br/>

OAS 3는 2017년 7월에 발표된 spec이며, 많은 개선이 이루어졌다고 한다. 해당 spec은 JSON, YAML을 통하여 작성할 수 있다. documentation generation tools 를 사용하여서 해당 API의 기능, 테스트 코드 등을 사용자(클라이언트 개발자) 입장에서 쉽게 알 수 있게 해준다.

- Spring fox : OAS 2를 지원하는 라이브러리이다.
- Springdoc-openapi v1.5.1 : OAS 3를 지원하는 라이브러리이다.

    [https://github.com/springdoc/springdoc-openapi](https://github.com/springdoc/springdoc-openapi)

<br/>

### Format

- **키 값에 대해 대소문자를 구분하며** 특별한 경우에는 구분하지 않는다고 명시해야 한다.
- spec 의 schema는 고정된 필드 이름(식별자와)과 문자열, 패턴 값을 가진다.

    ```yaml
    #식별자 / 문자열 or 패턴 값
    "field": [ 1, 2, 3 ]
    ```

- (Json) 사용되는 Tag는 **JSON Schema ruleset** 에 정의된 것들만 사용해야 한다.
- YAML 에서 사용되는 키 값은 **YAML Failsafe schema ruleset** 를 무조건 따라야 한다.
- API는 YAML 또는 JSON 형식으로 문서에 정의 될 수 있지만, API 요청 및 응답 본문과 콘텐츠가 JSON 또는 YAML 일 필요는 없다.

<br/>

### Data Type

**OAS의 타입은 JSON Schema Specification Wright Draft 00 의 정의된 유형을 기반으로 한다.**

- Integer → int 32
- → int 64
- number → float
- → double
- string
- → byte
- → binary
- → date
- → date-time
- → password
- boolean

<br/>

### Rich Text Formatting

OAS는 description필드 전체에서 **CommonMark** 라는 기본적인 마크 다운 형식을 지원한다.

- 어떠한 텍스트 서식들을 렌더링하는 경우에는 최소한의 마크다운 구문을 지원해야한다.

<br/>

### Relative References in URLs

URL의 기본적인 속성은 모두 **RFC3986**에 정의 된 상대 참조를 사용한다.

- Server Object 형식을 사용하거나 Reference Object 형식을 사용한다.

    ```yaml
    # Server Object
    # url, desciption, variables
    # Server에 대한 값을 나타내는 Object이다. (client에 제공되는)
    {
      "url": "https://development.gigantic-server.com/v1",
      "description": "Development server"
    }
    # 복수의 Server를 나타내는 경우
    {
      "servers": [
        {
          "url": "https://development.gigantic-server.com/v1",
          "description": "Development server"
        },
        {
          "url": "https://staging.gigantic-server.com/v1",
          "description": "Staging server"
        },
        {
          "url": "https://api.gigantic-server.com/v1",
          "description": "Production server"
        }
      ]
    }
    # port의 대한 값이나, default 설정, BasePath 등의 정보도 제공 가능하다.
    "port": {
              "enum": [
                "8443",
                "443"
              ],
              "default": "8443"
            },
            "basePath": {
              "default": "v2"
            }

    # Reference Object
    # $ref 
    # spec의 다른 component를 내부, 외부에서 참조하게끔 도와주는 Object 이다.
    {
    	"$ref": "#/components/schemas/Pet"
    }
    ```

<br/>

### Example Object

요청, 응답, 매개 변수에 대한 예시를 보여준다.

```yaml
# 요청 본문
requestBody:
  content:
    'application/json':
      schema:
        $ref: '#/components/schemas/Address'
      examples: 
        foo:
          summary: A foo example
          value: {"foo": "bar"}
        bar:
          summary: A bar example
          value: {"bar": "baz"}
    'application/xml':
      examples: 
        xmlExample:
          summary: This is an example in XML
          externalValue: 'http://example.org/examples/address-example.xml'
    'text/plain':
      examples:
        textExample: 
          summary: This is a text example
          externalValue: 'http://foo.bar/examples/address-example.txt'

# 응답 본문
responses:
  '200':
    description: your car appointment has been booked
    content: 
      application/json:
        schema:
          $ref: '#/components/schemas/SuccessResponse'
        examples:
          confirmation-success:
            $ref: '#/components/examples/confirmation-success'

# 매개 변수 
parameters:
  - name: 'zipCode'
    in: 'query'
    schema:
      type: 'string'
      format: 'zip-code'
    examples:
      zip-example: 
        $ref: '#/components/examples/zip-example'
```

<br/>

### Another Obejcts

- 그 외에도 수많은 객체들이 존재한다.

    ```yaml
    - Info Object - API에 대한 메타 데이터를 제공한다/
    {
      "title": "Sample Pet Store App",
      "description": "This is a sample server for a pet store.",
      "termsOfService": "http://example.com/terms/",
      "contact": {
        "name": "API Support",
        "url": "http://www.example.com/support",
        "email": "support@example.com"
      },
      "license": {
        "name": "Apache 2.0",
        "url": "https://www.apache.org/licenses/LICENSE-2.0.html"
      },
      "version": "1.0.1"
    }

    - Contact Object - API와 관련된 연락처 정보를 나타낸다
    {
      "name": "API Support",
      "url": "http://www.example.com/support",
      "email": "support@example.com"
    }

    - License Object - Lincense 정보를 나타낸다.
    {
      "name": "Apache 2.0",
      "url": "https://www.apache.org/licenses/LICENSE-2.0.html"
    }

    - Server Variable Object - 서버의 변수 정보를 나타낸다.

    - Component Object - OAS에서 제공하는 재사용 가능한 객체들의 집합이다.
    "components": {
      "schemas": {
        "GeneralError": {
          "type": "object",
          "properties": {
            "code": {
              "type": "integer",
              "format": "int32"
            },
            "message": {
              "type": "string"
            }
          }
        },
        "Category": {
          "type": "object",
          "properties": {
            "id": {
              "type": "integer",
              "format": "int64"
            },
            "name": {
              "type": "string"
            }
          }
        },
        "Tag": {
          "type": "object",
          "properties": {
            "id": {
              "type": "integer",
              "format": "int64"
            },
            "name": {
              "type": "string"
            }
          }
        }
      },
      "parameters": {
        "skipParam": {
          "name": "skip",
          "in": "query",
          "description": "number of items to skip",
          "required": true,
          "schema": {
            "type": "integer",
            "format": "int32"
          }
        },
        "limitParam": {
          "name": "limit",
          "in": "query",
          "description": "max records to return",
          "required": true,
          "schema" : {
            "type": "integer",
            "format": "int32"
          }
        }
      },
      "responses": {
        "NotFound": {
          "description": "Entity not found."
        },
        "IllegalInput": {
          "description": "Illegal input for operation."
        },
        "GeneralError": {
          "description": "General Error",
          "content": {
            "application/json": {
              "schema": {
                "$ref": "#/components/schemas/GeneralError"
              }
            }
          }
        }
      },
      "securitySchemes": {
        "api_key": {
          "type": "apiKey",
          "name": "api_key",
          "in": "header"
        },
        "petstore_auth": {
          "type": "oauth2",
          "flows": {
            "implicit": {
              "authorizationUrl": "http://example.org/api/oauth/dialog",
              "scopes": {
                "write:pets": "modify pets in your account",
                "read:pets": "read your pets"
              }
            }
          }
        }
      }
    }

    - Path Object - API의 End point 나 어떤 작업에 대한 경로를 나타낸다.
    {
      "/pets": {
        "get": {
          "description": "Returns all pets from the system that the user has access to",
          "responses": {
            "200": {          
              "description": "A list of pets.",
              "content": {
                "application/json": {
                  "schema": {
                    "type": "array",
                    "items": {
                      "$ref": "#/components/schemas/pet"
                    }
                  }
                }
              }
            }
          }
        }
      }
    }

    - Path Item Object 
    - Operation Object
    - External Documentation Object
    - Parameter Object
    - Request Body Object
    - Media Type Object
    - Encoding Object
    - Responses Object
    - Response Object
    - Callback Object

    ```

<br/>


## springdoc-openapi. 사용법

<br/>

**Gradle 의존성** 

```java
implementation("org.springdoc:springdoc-openapi-ui:1.4.6")
```

<br/>

**Java Configuration 추가하기**

```java
@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
                .components(new Components())
                .info(new Info().title("XXX Application API").description(
                        "XXX Application Descrptions.."));
    }
}
```

<br/>

**Sample Controller**

```java
@RestController
@RequestMapplng(path = "/posts")
@Tag(name = "Domain API Names", description = "API Description")
public class HelloController {

		@Operation(summary = "Find User by id", description = "ID serach by ID = {id} format"
							,tags = {"Domain API Names"})
		@ApiResponse(responseCode = "200", desription = "Status Code Description",
							, content = (array = @ArraySchema(schema = @Schema(implementation = Post.class)))
		@GetMapping(value = "/{postId}", produces = { "application/json", "application/xml" })
		public ResponseEntity<List<Contact>> findById(
				@Parameter(description="Id of the Post for search.") @Pathvariable Long postId){
				Post postsById = PostService.findById(postId);
				return ResponseEntity.status(HttpStatus.OK).body(postsById);
		}
}
```

<br/>

### **Open API Annotation** 

<br/>

**@Tag ( spring fox = @Api )** 

- name : 컨트롤러의 도메인 이름 (PostController의 경우 Post)
- description : Name에 대한 설명

**@Operation ( spring fox = @ApiOperation)**

- summary :  해당 메서드의 동작 정보 요약
- description : 해당 동작의 설명
- tags : 상단에 정의된 Tag의 name과 동일하게 주는 값

**@ApiResponse**

- responseCode : 성공시 전달할 HTTP Status Code
- desription : 해당 Code 에 대한 설명
- content : 응답되는 컨텐츠 유형 설정
    - array : 단일이 아닌 복수의 자원 응답이 있을 경우 사용

**@ArraySchema**

- schema : 복수 자원 응답시 내부에 저장되는 리소스 설정

**@Schema ( spring fox = @ApiModel, @ApiModelProperty)**

- implementation : 반환되는 DTO, Entity 를 .class 형식으로 연동 (구조 상속)

**@Parameter ( spring fox = @ApiIgnore, @ApiImplicitParam, @ApiParam)**

- description : 해당 URL에 대한 Pathvariable, Query Parameter 설명

<br/>

### 참고, 예시코드 출처

- [https://swagger.io/specification/#license-object](https://swagger.io/specification/#license-object)