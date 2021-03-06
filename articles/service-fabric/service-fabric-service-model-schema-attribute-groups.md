---
title: Grupy atrybutów schematu XML modelu usługi
description: Opisuje grupy atrybutów w schemacie XML modelu usługi sieci szkieletowej usług.
ms.topic: reference
ms.date: 12/10/2018
ms.openlocfilehash: 95e19dbdeafdd03af56acaeeb06901a36f2e0333
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609387"
---
<!-- This article was generated by the Python script found in the service-fabric-service-model-schema.md file -->

# <a name="service-model-xml-schema-attribute-groups"></a>Grupy atrybutów schematu XML modelu usługi

## <a name="accountcredentialsgroup-attributegroup"></a>Grupa grup ekscesów grupy accountcredentials

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|Grupa AccountCredentials|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="AccountCredentialsGroup">
        <xs:attribute name="AccountName" type="xs:string" use="optional">
          <xs:annotation>
            <xs:documentation>User name or Service Account Name (for example, MyMachine\JohnDoe or John.Doe@department.contoso.com).</xs:documentation>
          </xs:annotation>
        </xs:attribute>
        <xs:attribute name="Password" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>Password for the user account.</xs:documentation>
            </xs:annotation>
        </xs:attribute>
    </xs:attributeGroup>
    
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="accountname"></a>AccountName
Nazwa użytkownika lub nazwa konta usługi (na przykład MyMachine\JohnDoe lub John.Doe@department.contoso.com).

|Atrybut|Wartość|
|---|---|
|name|AccountName|
|type|xs:ciąg znaków|
|używanie|optional|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="AccountName" type="xs:string" use="optional">
          <xs:annotation>
            <xs:documentation>User name or Service Account Name (for example, MyMachine\JohnDoe or John.Doe@department.contoso.com).</xs:documentation>
          </xs:annotation>
        </xs:attribute>
        
```

#### <a name="password"></a>Hasło
Hasło do konta użytkownika.

|Atrybut|Wartość|
|---|---|
|name|Hasło|
|type|xs:ciąg znaków|
|używanie|optional|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Password" type="xs:string" use="optional">
            <xs:annotation>
                <xs:documentation>Password for the user account.</xs:documentation>
            </xs:annotation>
        </xs:attribute>
    
```

## <a name="applicationinstanceattrgroup-attributegroup"></a>Atrybut Application InstanceAttrGroupGroup
Grupa atrybutów dla wystąpienia aplikacji.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|Grupa InstanceAttrGroup aplikacji|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationInstanceAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for application instance.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="NameUri" type="FabricUri" use="required">
      <xs:annotation>
        <xs:documentation>Fully qualified name of the application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    <xs:attribute name="ApplicationId" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>Id of this application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="nameuri"></a>NazwaUri
W pełni kwalifikowana nazwa aplikacji.

|Atrybut|Wartość|
|---|---|
|name|NazwaUri|
|type|FabricUri ( FabricUri )|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="NameUri" type="FabricUri" use="required">
      <xs:annotation>
        <xs:documentation>Fully qualified name of the application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    
```

#### <a name="applicationid"></a>ApplicationId
Identyfikator tej aplikacji.

|Atrybut|Wartość|
|---|---|
|name|ApplicationId|
|type|xs:ciąg znaków|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationId" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>Id of this application.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="applicationmanifestattrgroup-attributegroup"></a>ApplicationManifestAttrGroup attributeGroup
Grupa atrybutów dla manifestu aplikacji.

|Atrybut|Wartość|
|---|---|
|content|3 atrybuty|
|name|AplikacjaManifestAttrGroup|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationManifestAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for application manifest.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ApplicationTypeName" use="required">
      <xs:annotation>
        <xs:documentation>The type identifier for this application.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ApplicationTypeVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of this application type, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ManifestId" use="optional" default="" type="xs:string">
      <xs:annotation>
        <xs:documentation>The identifier of this application manifest, an unstructured string.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    <xs:anyAttribute processContents="skip"/> <!-- Allow unknown attributes to be used. -->
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="applicationtypename"></a>ApplicationTypeName
Identyfikator typu dla tej aplikacji.

|Atrybut|Wartość|
|---|---|
|name|ApplicationTypeName|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationTypeName" use="required">
      <xs:annotation>
        <xs:documentation>The type identifier for this application.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="applicationtypeversion"></a>ApplicationTypeVersion (Wersja typu aplikacji)
Wersja tego typu aplikacji, ciąg bez struktury.

|Atrybut|Wartość|
|---|---|
|name|ApplicationTypeVersion (Wersja typu aplikacji)|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ApplicationTypeVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of this application type, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="manifestid"></a>ManifestId
Identyfikator tego manifestu aplikacji, ciąg bez struktury.

|Atrybut|Wartość|
|---|---|
|name|ManifestId|
|używanie|optional|
|default||
|type|xs:ciąg znaków|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ManifestId" use="optional" default="" type="xs:string">
      <xs:annotation>
        <xs:documentation>The identifier of this application manifest, an unstructured string.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
    
```

## <a name="configoverridesidentifier-attributegroup"></a>ConfigOverridesIdentifier attributeGroup (Grupa identyfikatora)
Identyfikuje zastąpienia konfiguracji dla pakietu usług.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|ConfigOverridesIdentifier (ConfigOverridesIdentifier)|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConfigOverridesIdentifier">
    <xs:annotation>
      <xs:documentation>Identifies configuration overrides for a service package.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ServicePackageName" type="xs:string" use="required"/>
    <xs:attribute name="RolloutVersion" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>ID of the rollout in which changes were made to the overrides element.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="servicepackagename"></a>Nazwa pakietu usługi

|Atrybut|Wartość|
|---|---|
|name|Nazwa pakietu usługi|
|type|xs:ciąg znaków|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServicePackageName" type="xs:string" use="required"/>
    
```

#### <a name="rolloutversion"></a>WdrażanieVersion
Identyfikator wdrożenia, w którym wprowadzono zmiany w elemencie overrides.

|Atrybut|Wartość|
|---|---|
|name|WdrażanieVersion|
|type|xs:ciąg znaków|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RolloutVersion" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>ID of the rollout in which changes were made to the overrides element.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="connectionstring-attributegroup"></a>ConnectionString attributeGroup

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|Connectionstring|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConnectionString">
                <xs:attribute name="ConnectionString" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Connection string to the Azure storage account. Format: DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="connectionstring"></a>Connectionstring
Parametry połączenia z kontem magazynu platformy Azure. Format: DefaultEndpointsProtocol=https; AccountName=[]; AccountKey=[]

|Atrybut|Wartość|
|---|---|
|name|Connectionstring|
|type|xs:ciąg znaków|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ConnectionString" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Connection string to the Azure storage account. Format: DefaultEndpointsProtocol=https;AccountName=[];AccountKey=[]</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="containername-attributegroup"></a>ContainerName attributeGroup

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|NazwaKontenera|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ContainerName">
    <xs:attribute name="ContainerName" type="xs:string">
      <xs:annotation>
        <xs:documentation>The name of the container in Azure blob storage where data is uploaded.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="containername"></a>NazwaKontenera
Nazwa kontenera w magazynie obiektów blob platformy Azure, w którym są przekazywane dane.

|Atrybut|Wartość|
|---|---|
|name|NazwaKontenera|
|type|xs:ciąg znaków|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ContainerName" type="xs:string">
      <xs:annotation>
        <xs:documentation>The name of the container in Azure blob storage where data is uploaded.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="datadeletionageindays-attributegroup"></a>DataDeletionAgeInDays attributeGroup

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|DataDeletionAgeInDays|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="DataDeletionAgeInDays">
    <xs:attribute name="DataDeletionAgeInDays" type="xs:string">
      <xs:annotation>
        <xs:documentation>Number of days after which old data is deleted from this location.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="datadeletionageindays"></a>DataDeletionAgeInDays
Liczba dni, po których stare dane są usuwane z tej lokalizacji.

|Atrybut|Wartość|
|---|---|
|name|DataDeletionAgeInDays|
|type|xs:ciąg znaków|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="DataDeletionAgeInDays" type="xs:string">
      <xs:annotation>
        <xs:documentation>Number of days after which old data is deleted from this location.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="isenabled-attributegroup"></a>Grupa atrybutów IsEnabled

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|IsEnabled|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="IsEnabled">
                <xs:attribute name="IsEnabled" type="xs:string">
                        <xs:annotation>
                                <xs:documentation>Whether or not data transfer to this destination is enabled. By default, it is not enabled.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="isenabled"></a>IsEnabled
Określa, czy transfer danych do tego miejsca docelowego jest włączony. Domyślnie nie jest włączona.

|Atrybut|Wartość|
|---|---|
|name|IsEnabled|
|type|xs:ciąg znaków|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="IsEnabled" type="xs:string">
                        <xs:annotation>
                                <xs:documentation>Whether or not data transfer to this destination is enabled. By default, it is not enabled.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="levelfilter-attributegroup"></a>LevelFilter attributeGroup (Grupa atrybutów LevelFilter)

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|Filtr poziomu|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="LevelFilter">
    <xs:attribute name="LevelFilter" type="xs:string">
      <xs:annotation>
        <xs:documentation>Level at which ETW events should be filtered. All events at the same or lower level than the specified level are included.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="levelfilter"></a>Filtr poziomu
Poziom, na którym zdarzenia ETW powinny być filtrowane. Wszystkie zdarzenia na tym samym lub niższym poziomie niż określony poziom są uwzględniane.

|Atrybut|Wartość|
|---|---|
|name|Filtr poziomu|
|type|xs:ciąg znaków|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="LevelFilter" type="xs:string">
      <xs:annotation>
        <xs:documentation>Level at which ETW events should be filtered. All events at the same or lower level than the specified level are included.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="namevaluepair-attributegroup"></a>NameValuePair attributeGroup
Nazwa i wartość zdefiniowane jako atrybut.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|Namevaluepair|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="NameValuePair">
    <xs:annotation>
      <xs:documentation>Name and Value defined as an attribute.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="Name" use="required">
      <xs:annotation>
        <xs:documentation>The name of the setting to override.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="Value" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>The new value of the setting.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="name"></a>Nazwa
Nazwa ustawienia do zastąpienia.

|Atrybut|Wartość|
|---|---|
|name|Nazwa|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Name" use="required">
      <xs:annotation>
        <xs:documentation>The name of the setting to override.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="value"></a>Wartość
Nowa wartość ustawienia.

|Atrybut|Wartość|
|---|---|
|name|Wartość|
|type|xs:ciąg znaków|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Value" type="xs:string" use="required">
      <xs:annotation>
        <xs:documentation>The new value of the setting.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="path-attributegroup"></a>Grupa atrybutów ścieżki

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|Ścieżka|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Path">
                <xs:attribute name="Path" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the file share. Format: file:[]</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="path"></a>Ścieżka
Ścieżka do udziału plików. Format: plik:[]

|Atrybut|Wartość|
|---|---|
|name|Ścieżka|
|type|xs:ciąg znaków|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Path" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the file share. Format: file:[]</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="relativefolderpath-attributegroup"></a>Grupa atrybutów RelativeFolderPath

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|Ścieżka względnafolderu|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RelativeFolderPath">
                <xs:attribute name="RelativeFolderPath" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the folder, relative to the application log directory.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        </xs:attributeGroup>
        
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="relativefolderpath"></a>Ścieżka względnafolderu
Ścieżka do folderu względem katalogu dziennika aplikacji.

|Atrybut|Wartość|
|---|---|
|name|Ścieżka względnafolderu|
|type|xs:ciąg znaków|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RelativeFolderPath" type="xs:string" use="required">
                        <xs:annotation>
                                <xs:documentation>Path to the folder, relative to the application log directory.</xs:documentation>
                        </xs:annotation>
                </xs:attribute>
        
```

## <a name="servicemanifestidentifier-attributegroup"></a>Atrybut ServiceManifestIdentifierGroup
Identyfikuje manifest usługi.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|ServiceManifestIdentifier|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestIdentifier">
    <xs:annotation>
      <xs:documentation>Identifies a service manifest.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="ServiceManifestName" use="required">
      <xs:annotation>
        <xs:documentation>The name of the service manifest this is referenced. The name must match the Name declared in the ServiceManifest element of the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="ServiceManifestVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of the service manifest that is referenced. The version must match the version declared in the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="servicemanifestname"></a>Nazwa usługi
Nazwa manifestu usługi odwołuje się do tej. Nazwa musi być zgodna name zadeklarowane w ServiceManifest element manifestu usługi.

|Atrybut|Wartość|
|---|---|
|name|Nazwa usługi|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestName" use="required">
      <xs:annotation>
        <xs:documentation>The name of the service manifest this is referenced. The name must match the Name declared in the ServiceManifest element of the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="servicemanifestversion"></a>UsługaWyjaszość
Wersja manifestu usługi, do którego się odwołuje. Wersja musi być zgodna z wersją zadeklarowaną w manifeście usługi.

|Atrybut|Wartość|
|---|---|
|name|UsługaWyjaszość|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="ServiceManifestVersion" use="required">
      <xs:annotation>
        <xs:documentation>The version of the service manifest that is referenced. The version must match the version declared in the service manifest.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  
```

## <a name="uploadintervalinminutes-attributegroup"></a>Grupa atrybutów UploadIntervalInMinutes

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|PrzekazywanieIntervalInMinutes|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="UploadIntervalInMinutes">
    <xs:attribute name="UploadIntervalInMinutes" type="xs:string">
      <xs:annotation>
        <xs:documentation>Interval in minutes at which data is uploaded to this destination.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="uploadintervalinminutes"></a>PrzekazywanieIntervalInMinutes
Interwał w minutach, w którym dane są przekazywane do tego miejsca docelowego.

|Atrybut|Wartość|
|---|---|
|name|PrzekazywanieIntervalInMinutes|
|type|xs:ciąg znaków|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="UploadIntervalInMinutes" type="xs:string">
      <xs:annotation>
        <xs:documentation>Interval in minutes at which data is uploaded to this destination.</xs:documentation>
      </xs:annotation>
    </xs:attribute>
  
```

## <a name="versioneditemattrgroup-attributegroup"></a>Grupa atrybutów VersionedItemAttrGroup
Grupa atrybutów dla sekcji przechowywania wersji w dokumentach ApplicationInstance i ServicePackage.

|Atrybut|Wartość|
|---|---|
|content|1 atrybut(-y)|
|name|WersjaedItemAttrGroup|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="VersionedItemAttrGroup">
    <xs:annotation>
      <xs:documentation>Attribute group for versioning sections in ApplicationInstance and ServicePackage documents.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="RolloutVersion" type="xs:string" use="required"/>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="rolloutversion"></a>WdrażanieVersion

|Atrybut|Wartość|
|---|---|
|name|WdrażanieVersion|
|type|xs:ciąg znaków|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="RolloutVersion" type="xs:string" use="required"/>
  
```

## <a name="versionedname-attributegroup"></a>Grupa atrybutów VersionedName
Grupa atrybutów zawierająca nazwę i wersję.

|Atrybut|Wartość|
|---|---|
|content|2 atrybuty|
|name|Nazwa wersji|

### <a name="xml-source"></a>Źródło XML
```xml
<xs:attributeGroup xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="VersionedName">
    <xs:annotation>
      <xs:documentation>Attribute group that includes a Name and a Version.</xs:documentation>
    </xs:annotation>
    <xs:attribute name="Name" use="required">
      <xs:annotation>
        <xs:documentation>Name of the versioned item.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="Version" use="required">
      <xs:annotation>
        <xs:documentation>Version of the versioned item, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  </xs:attributeGroup>
  
```
### <a name="attribute-details"></a>Szczegóły atrybutu

#### <a name="name"></a>Nazwa
Nazwa elementu wersjonatowanego.

|Atrybut|Wartość|
|---|---|
|name|Nazwa|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Name" use="required">
      <xs:annotation>
        <xs:documentation>Name of the versioned item.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    
```

#### <a name="version"></a>Wersja
Wersja elementu wersjonawce, ciąg bez struktury.

|Atrybut|Wartość|
|---|---|
|name|Wersja|
|używanie|wymagany|

##### <a name="xml-source"></a>Źródło XML
```xml
<xs:attribute xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/2011/01/fabric" name="Version" use="required">
      <xs:annotation>
        <xs:documentation>Version of the versioned item, an unstructured string.</xs:documentation>
      </xs:annotation>
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:minLength value="1"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
  
```

