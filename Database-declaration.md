# **Project's Database**

___

## **1 - Model**
The database used in the project is currently setup like the model below.

![image.png](/.attachments/image-5ece2389-9366-4dcf-8216-c158cc9a3d18.png)

___

## **2 - Summary**
- 1 - [Model](#**1---model**)
- 3 - [Table declarations](#**3---table-declarations**)
  - [Address](#**--address-table**)
  - [Certification](#**--certification-table**)
  - [Certification Path](#**--certificationpath-table**)
  - [Certification Skill Coverage](#**--certificationskillcoverage-table**)
  - [Country/Region](#**--countryregion-table**)
  - [Customer](#**--customer-table**)
  - [Trainer](#**--trainer-table**)
  - [Training](#**--Training-table**)
  - [Training Content](#**--trainingcontent-table**)
  - [Intervention](#**--intervention-table**)
  - [Level](#**--level-table**)
  - [OrderDetail](#**--orderdetail-table**)
  - [OrderHeader](#**--orderheader-table**)
  - [Path](#**--path-table**)
  - [Person](#**--person-table**)
  - [Product](#**--product-table**)
  - [Sales Person](#**--salesperson-table**)
  - [Sales Territory](#**--salesterritory-table**)
  - [Session](#**--session-table**)
  - [Skill](#**--skill-table**)
  - [State/Province](#**--stateprovince-table**)
- 4 - [Contraints](#**4---contraints**)
- 5 - [Changelog](#**5---changelog**)

___

## **3 - Table declarations**

In this part will be list all table declarations contained in the database.

### **- Address table**

Contain the bill contact details for each [Order header](#**--orderheader-table**).
Require one to two postal address with their related [Country](#**--stateprovince-table**).

~~~~sql
/****** Object:  Table [dbo].[Address]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Address](
	[AddressID] [int] IDENTITY(1,1) NOT FOR REPLICATION NOT NULL,
	[AddressLine1] [nvarchar](60) NOT NULL,
	[AddressLine2] [nvarchar](60) NULL,
	[City] [nvarchar](30) NOT NULL,
	[StateProvinceID] [int] NOT NULL,
	[PostalCode] [nvarchar](15) NOT NULL,
	[SpatialLocation] [geography] NULL,
	[ModifiedDate] [datetime] NOT NULL,
 CONSTRAINT [PK_Address_AddressID] PRIMARY KEY CLUSTERED 
(
	[AddressID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
GO
~~~~

### **- Certification Table**

Contain the available certification details with its description, its [Level](#**--level-table**) and its minimum score required.

~~~~sql
/****** Object:  Table [dbo].[Certification]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Certification](
	[CertifID] [int] NOT NULL,
	[Description] [nvarchar](250) NULL,
	[LevelID] [int] NULL,
	[MinimumScore] [int] NULL,
 CONSTRAINT [PK_Certification] PRIMARY KEY CLUSTERED 
(
	[CertifID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- CertificationPath Table**

Contain a [Path](#**--path-table**) chain.

~~~~sql
/****** Object:  Table [dbo].[CertificationPath]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[CertificationPath](
	[CertificationID] [int] NOT NULL,
	[PathId] [int] NOT NULL,
 CONSTRAINT [PK_CertificationParcours] PRIMARY KEY CLUSTERED 
(
	[CertificationID] ASC,
	[PathId] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- CertificationSkillCoverage table**

Contain the skill coverage related to the linked [Certification](#**--certification-table**).

~~~~sql
/****** Object:  Table [dbo].[CertificationSkillCoverage]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[CertificationSkillCoverage](
	[CertificationID] [int] NOT NULL,
	[SkillID] [int] NOT NULL,
	[Percentage] [int] NULL,
 CONSTRAINT [PK_CertificationCompetence] PRIMARY KEY CLUSTERED 
(
	[CertificationID] ASC,
	[SkillID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- CountryRegion table**

Contain the country name list and their last modified date.

~~~~sql
/****** Object:  Table [dbo].[CountryRegion]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[CountryRegion](
	[CountryRegionCode] [nvarchar](3) NOT NULL,
	[Name] [nvarchar](50) NOT NULL,
	[ModifiedDate] [datetime] NOT NULL,
 CONSTRAINT [PK_CountryRegion_CountryRegionCode] PRIMARY KEY CLUSTERED 
(
	[CountryRegionCode] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Customer table**


Contain the customer informations related to the [Order header](#**--orderheader-table**).
Require the related [Person](#**--person-table**) and [SalesTerritory](#**--salesterritory-table**).

Note : Generate its [AccountNumber] based on its [CustomerID] generated.

~~~~sql
/****** Object:  Table [dbo].[Customer]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Customer](
	[CustomerID] [int] IDENTITY(1,1) NOT FOR REPLICATION NOT NULL,
	[PersonID] [int] NULL,
	[TerritoryID] [int] NULL,
	[AccountNumber]  AS (isnull('AW_DIIAGE_'+CONVERT([varchar](255),[CustomerID]),'')),
	[rowguid] [uniqueidentifier] ROWGUIDCOL  NOT NULL,
	[ModifiedDate] [datetime] NOT NULL,
 CONSTRAINT [PK_Customer_CustomerID] PRIMARY KEY CLUSTERED 
(
	[CustomerID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Trainer table**

Contain the trainer's personnal data and contact details.

~~~~sql
/****** Object:  Table [dbo].[Trainer]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Trainer](
	[TrainerID] [int] IDENTITY(1,1) NOT NULL,
	[FirstName] [varchar](50) NULL,
	[LastName] [varchar](50) NULL,
	[Address] [varchar](150) NULL,
	[Phone] [varchar](14) NULL,
	[EMail] [varchar](50) NULL,
 CONSTRAINT [PK_Trainer] PRIMARY KEY CLUSTERED 
(
	[TrainerID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Training table**

Contain the [Intervention](#**--trainingcontent-table**)'s and [TrainingContent](#**--trainingcontent-table**)'s data with the maximum places.

~~~~sql
/****** Object:  Table [dbo].[Training]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Training](
	[TrainingID] [int] IDENTITY(1,1) NOT NULL,
	[ThemeID] [int] NULL,
	[ModeLearningID] [int] NULL,
	[StaDate] [date] NULL,
	[PersonLimit] [int] NULL,
	[EndDate] [date] NULL,
 CONSTRAINT [PK_Training] PRIMARY KEY CLUSTERED 
(
	[TrainingID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- TrainingContent table**

Contain the training content with [Skill](#**--skill-table**) focused and the start/end dates planned.

~~~~sql
/****** Object:  Table [dbo].[TrainingContent]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[TrainingContent](
	[TrainingId] [int] NOT NULL,
	[SkillID] [int] NOT NULL,
	[StaDate] [date] NOT NULL,
	[EndDate] [date] NULL,
 CONSTRAINT [PK_TrainingContent] PRIMARY KEY CLUSTERED 
(
	[TrainingId] ASC,
	[SkillID] ASC,
	[StaDate] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Intervention table**

Contain the [Trainer](#**--trainer-table**) and the start/end dates.

~~~~sql
/****** Object:  Table [dbo].[Intervention]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Intervention](
	[TrainingID] [int] NOT NULL,
	[TrainerID] [int] NOT NULL,
	[Date] [date] NOT NULL,
	[StaDate] [time](7) NULL,
	[EndDate] [time](7) NULL,
 CONSTRAINT [PK_Intervention] PRIMARY KEY CLUSTERED 
(
	[TrainingID] ASC,
	[TrainerID] ASC,
	[Date] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Level table**

Contain the description of the associated level.

~~~~sql
/****** Object:  Table [dbo].[Level]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Level](
	[LevelID] [int] NOT NULL,
	[Description] [nvarchar](50) NULL,
 CONSTRAINT [PK_Niveau] PRIMARY KEY CLUSTERED 
(
	[LevelID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- OrderDetail table**

Contain the details of the [OrderHeader](#**--orderheader-table**) with the [Product](#**--product-table**) and its quantity.

~~~~sql
/****** Object:  Table [dbo].[OrderDetail]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[OrderDetail](
	[OrderID] [int] NOT NULL,
	[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
	[OrderQty] [smallint] NOT NULL,
	[ProductID] [int] NOT NULL,
	[UnitPrice] [money] NOT NULL,
	[LineTotal]  AS (isnull(([UnitPrice]*(1.0))*[OrderQty],(0.0))),
 CONSTRAINT [PK_OrderDetail_OrderID_OrderDetailID] PRIMARY KEY CLUSTERED 
(
	[OrderID] ASC,
	[OrderDetailID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- OrderHeader table**

Contain all the links of the order made.
Require its [OrderDetail](#**--orderdetail-table**), [Customer](#**--customer-table**), [SalesTerritory](#**--salesterritory-table**), [SalesPerson](#**--salesperson-table**) and bill [Address](#**--address-table**).

~~~~sql
/****** Object:  Table [dbo].[OrderHeader]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[OrderHeader](
	[SalesOrderID] [int] IDENTITY(1,1) NOT FOR REPLICATION NOT NULL,
	[RevisionNumber] [tinyint] NOT NULL,
	[OrderDate] [datetime] NOT NULL,
	[DueDate] [datetime] NOT NULL,
	[Status] [tinyint] NOT NULL,
	[SalesOrderNumber]  AS (isnull(N'SO'+CONVERT([nvarchar](23),[SalesOrderID]),N'*** ERROR ***')),
	[CustomerID] [int] NOT NULL,
	[SalesPersonID] [int] NULL,
	[TerritoryID] [int] NULL,
	[BillToAddressID] [int] NOT NULL,
	[SubTotal] [money] NOT NULL,
	[TaxAmt] [money] NOT NULL,
	[Reduc] [money] NOT NULL,
	[TotalDue]  AS (isnull(([SubTotal]+[TaxAmt])+[Reduc],(0))),
	[Comment] [nvarchar](128) NULL,
	[ModifiedDate] [datetime] NOT NULL,
 CONSTRAINT [PK_OrderHeader_SalesOrderID] PRIMARY KEY CLUSTERED 
(
	[SalesOrderID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Path table**

_I don't have a clue_

~~~~sql
/****** Object:  Table [dbo].[Path]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Path](
	[PathID] [int] IDENTITY(1,1) NOT NULL,
	[Description] [nvarchar](50) NULL,
	[ParentPathId] [int] NULL,
 CONSTRAINT [PK_Parcours] PRIMARY KEY CLUSTERED 
(
	[PathID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Person table**

Contain the person's data needed to register him.

~~~~sql
/****** Object:  Table [dbo].[Person]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Person](
	[BusinessEntityID] [int] NOT NULL,
	[PersonType] [nchar](2) NOT NULL,
	[Title] [nvarchar](8) NULL,
	[FirstName] [nvarchar](50) NOT NULL,
	[MiddleName] [nvarchar](50) NULL,
	[LastName] [nvarchar](50) NOT NULL,
	[Suffix] [nvarchar](10) NULL,
 CONSTRAINT [PK_Person_BusinessEntityID] PRIMARY KEY CLUSTERED 
(
	[BusinessEntityID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Product table**

Contain the [Certification](#**--certification-table**) and its related [Training](#**--training-table**).

~~~~sql
/****** Object:  Table [dbo].[Product]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Product](
	[ProductID] [int] NOT NULL,
	[CertifID] [int] NULL,
	[TrainingID] [int] NULL,
	[Description] [nvarchar](150) NULL,
 CONSTRAINT [PK_Product] PRIMARY KEY CLUSTERED 
(
	[ProductID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- SalesPerson table**

Contain the seller's information and a link to its [SalesTerritory](#**--salesterritory-table**).

~~~~sql
/****** Object:  Table [dbo].[SalesPerson]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[SalesPerson](
	[BusinessEntityID] [int] NOT NULL,
	[TerritoryID] [int] NULL,
	[SalesQuota] [money] NULL,
	[Bonus] [money] NOT NULL,
	[CommissionPct] [smallmoney] NOT NULL,
	[SalesYTD] [money] NOT NULL,
	[SalesLastYear] [money] NOT NULL,
 CONSTRAINT [PK_SalesPerson_BusinessEntityID] PRIMARY KEY CLUSTERED 
(
	[BusinessEntityID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- SalesTerritory table**

Contain the sales information of the [StateProvince](#**--stateprovince-table**).

~~~~sql
/****** Object:  Table [dbo].[SalesTerritory]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[SalesTerritory](
	[TerritoryID] [int] IDENTITY(1,1) NOT NULL,
	[Name] [varchar](50) NOT NULL,
	[CountryRegionCode] [nvarchar](3) NOT NULL,
	[Group] [nvarchar](50) NOT NULL,
	[SalesYTD] [money] NOT NULL,
	[SalesLastYear] [money] NOT NULL,
	[CostYTD] [money] NOT NULL,
	[CostLastYear] [money] NOT NULL,
	[rowguid] [uniqueidentifier] ROWGUIDCOL  NOT NULL,
	[ModifiedDate] [datetime] NOT NULL,
 CONSTRAINT [PK_SalesTerritory_TerritoryID] PRIMARY KEY CLUSTERED 
(
	[TerritoryID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Session table**

_I don't have a clue._

~~~~sql
/****** Object:  Table [dbo].[Session]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Session](
	[PersonID] [int] NOT NULL,
	[CertificationID] [int] NOT NULL,
	[StaDate] [datetime] NOT NULL,
	[Result] [int] NULL,
	[MockExamDuration] [int] NULL,
 CONSTRAINT [PK_TestPass] PRIMARY KEY CLUSTERED 
(
	[PersonID] ASC,
	[CertificationID] ASC,
	[StaDate] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- Skill table**

Contain the the skill's title and its description.

~~~~sql
/****** Object:  Table [dbo].[Skill]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[Skill](
	[SkillID] [int] IDENTITY(1,1) NOT NULL,
	[Title] [nvarchar](50) NULL,
	[Description] [nvarchar](250) NULL,
 CONSTRAINT [PK_Competence] PRIMARY KEY CLUSTERED 
(
	[SkillID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

### **- StateProvince table**

Contain the state/province information.

~~~~sql
/****** Object:  Table [dbo].[StateProvince]    Script Date: 14/12/2020 08:16:36 ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[StateProvince](
	[StateProvinceID] [int] IDENTITY(1,1) NOT NULL,
	[StateProvinceCode] [nchar](3) NOT NULL,
	[CountryRegionCode] [nvarchar](3) NOT NULL,
	[Name] [varchar](50) NOT NULL,
	[TerritoryID] [int] NOT NULL,
	[rowguid] [uniqueidentifier] ROWGUIDCOL  NOT NULL,
	[ModifiedDate] [datetime] NOT NULL,
 CONSTRAINT [PK_StateProvince_StateProvinceID] PRIMARY KEY CLUSTERED 
(
	[StateProvinceID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO
~~~~

___

## **4 - Contraints**

The script also define constraints to several columns to prevent irrelevant data with the script below.

~~~~sql
ALTER TABLE [dbo].[Address] ADD  CONSTRAINT [DF_Address_ModifiedDate]  DEFAULT (getdate()) FOR [ModifiedDate]
GO
ALTER TABLE [dbo].[CountryRegion] ADD  CONSTRAINT [DF_CountryRegion_ModifiedDate]  DEFAULT (getdate()) FOR [ModifiedDate]
GO
ALTER TABLE [dbo].[Customer] ADD  CONSTRAINT [DF_Customer_rowguid]  DEFAULT (newid()) FOR [rowguid]
GO
ALTER TABLE [dbo].[Customer] ADD  CONSTRAINT [DF_Customer_ModifiedDate]  DEFAULT (getdate()) FOR [ModifiedDate]
GO
ALTER TABLE [dbo].[OrderHeader] ADD  CONSTRAINT [DF_OrderHeader_RevisionNumber]  DEFAULT ((0)) FOR [RevisionNumber]
GO
ALTER TABLE [dbo].[OrderHeader] ADD  CONSTRAINT [DF_OrderHeader_OrderDate]  DEFAULT (getdate()) FOR [OrderDate]
GO
ALTER TABLE [dbo].[OrderHeader] ADD  CONSTRAINT [DF_OrderHeader_Status]  DEFAULT ((1)) FOR [Status]
GO
ALTER TABLE [dbo].[OrderHeader] ADD  CONSTRAINT [DF_OrderHeader_SubTotal]  DEFAULT ((0.00)) FOR [SubTotal]
GO
ALTER TABLE [dbo].[OrderHeader] ADD  CONSTRAINT [DF_OrderHeader_TaxAmt]  DEFAULT ((0.00)) FOR [TaxAmt]
GO
ALTER TABLE [dbo].[OrderHeader] ADD  CONSTRAINT [DF_OrderHeader_Reduc]  DEFAULT ((0.00)) FOR [Reduc]
GO
ALTER TABLE [dbo].[OrderHeader] ADD  CONSTRAINT [DF_OrderHeader_ModifiedDate]  DEFAULT (getdate()) FOR [ModifiedDate]
GO
ALTER TABLE [dbo].[SalesPerson] ADD  CONSTRAINT [DF_SalesPerson_Bonus]  DEFAULT ((0.00)) FOR [Bonus]
GO
ALTER TABLE [dbo].[SalesPerson] ADD  CONSTRAINT [DF_SalesPerson_CommissionPct]  DEFAULT ((0.00)) FOR [CommissionPct]
GO
ALTER TABLE [dbo].[SalesPerson] ADD  CONSTRAINT [DF_SalesPerson_SalesYTD]  DEFAULT ((0.00)) FOR [SalesYTD]
GO
ALTER TABLE [dbo].[SalesPerson] ADD  CONSTRAINT [DF_SalesPerson_SalesLastYear]  DEFAULT ((0.00)) FOR [SalesLastYear]
GO
ALTER TABLE [dbo].[SalesTerritory] ADD  CONSTRAINT [DF_SalesTerritory_SalesYTD]  DEFAULT ((0.00)) FOR [SalesYTD]
GO
ALTER TABLE [dbo].[SalesTerritory] ADD  CONSTRAINT [DF_SalesTerritory_SalesLastYear]  DEFAULT ((0.00)) FOR [SalesLastYear]
GO
ALTER TABLE [dbo].[SalesTerritory] ADD  CONSTRAINT [DF_SalesTerritory_CostYTD]  DEFAULT ((0.00)) FOR [CostYTD]
GO
ALTER TABLE [dbo].[SalesTerritory] ADD  CONSTRAINT [DF_SalesTerritory_CostLastYear]  DEFAULT ((0.00)) FOR [CostLastYear]
GO
ALTER TABLE [dbo].[SalesTerritory] ADD  CONSTRAINT [DF_SalesTerritory_rowguid]  DEFAULT (newid()) FOR [rowguid]
GO
ALTER TABLE [dbo].[SalesTerritory] ADD  CONSTRAINT [DF_SalesTerritory_ModifiedDate]  DEFAULT (getdate()) FOR [ModifiedDate]
GO
ALTER TABLE [dbo].[StateProvince] ADD  CONSTRAINT [DF_StateProvince_rowguid]  DEFAULT (newid()) FOR [rowguid]
GO
ALTER TABLE [dbo].[StateProvince] ADD  CONSTRAINT [DF_StateProvince_ModifiedDate]  DEFAULT (getdate()) FOR [ModifiedDate]
GO
ALTER TABLE [dbo].[Address]  WITH CHECK ADD  CONSTRAINT [FK_Address_StateProvince_StateProvinceID] FOREIGN KEY([StateProvinceID])
REFERENCES [dbo].[StateProvince] ([StateProvinceID])
GO
ALTER TABLE [dbo].[Address] CHECK CONSTRAINT [FK_Address_StateProvince_StateProvinceID]
GO
ALTER TABLE [dbo].[Certification]  WITH NOCHECK ADD  CONSTRAINT [FK_Certification_Niveau] FOREIGN KEY([LevelID])
REFERENCES [dbo].[Level] ([LevelID])
GO
ALTER TABLE [dbo].[Certification] CHECK CONSTRAINT [FK_Certification_Niveau]
GO
ALTER TABLE [dbo].[CertificationPath]  WITH NOCHECK ADD  CONSTRAINT [FK_CertificationPath_Certification] FOREIGN KEY([CertificationID])
REFERENCES [dbo].[Certification] ([CertifID])
GO
ALTER TABLE [dbo].[CertificationPath] CHECK CONSTRAINT [FK_CertificationPath_Certification]
GO
ALTER TABLE [dbo].[CertificationPath]  WITH NOCHECK ADD  CONSTRAINT [FK_CertificationPath_Path] FOREIGN KEY([PathId])
REFERENCES [dbo].[Path] ([PathID])
GO
ALTER TABLE [dbo].[CertificationPath] CHECK CONSTRAINT [FK_CertificationPath_Path]
GO
ALTER TABLE [dbo].[CertificationSkillCoverage]  WITH NOCHECK ADD  CONSTRAINT [FK_CertificationCompetence_Competence] FOREIGN KEY([SkillID])
REFERENCES [dbo].[Skill] ([SkillID])
GO
ALTER TABLE [dbo].[CertificationSkillCoverage] CHECK CONSTRAINT [FK_CertificationCompetence_Competence]
GO
ALTER TABLE [dbo].[CertificationSkillCoverage]  WITH NOCHECK ADD  CONSTRAINT [FK_CertificationSkillCoverage_Certification] FOREIGN KEY([CertificationID])
REFERENCES [dbo].[Certification] ([CertifID])
GO
ALTER TABLE [dbo].[CertificationSkillCoverage] CHECK CONSTRAINT [FK_CertificationSkillCoverage_Certification]
GO
ALTER TABLE [dbo].[TrainingContent]  WITH CHECK ADD  CONSTRAINT [FK_TrainingContent_Formation] FOREIGN KEY([TrainingId])
REFERENCES [dbo].[Training] ([TrainingId])
GO
ALTER TABLE [dbo].[TrainingContent] CHECK CONSTRAINT [FK_TrainingContent_Formation]
GO
ALTER TABLE [dbo].[TrainingContent]  WITH CHECK ADD  CONSTRAINT [FK_TrainingContent_Skill] FOREIGN KEY([SkillID])
REFERENCES [dbo].[Skill] ([SkillID])
GO
ALTER TABLE [dbo].[TrainingContent] CHECK CONSTRAINT [FK_TrainingContent_Skill]
GO
ALTER TABLE [dbo].[Customer]  WITH CHECK ADD  CONSTRAINT [FK_Customer_Person_PersonID] FOREIGN KEY([PersonID])
REFERENCES [dbo].[Person] ([BusinessEntityID])
GO
ALTER TABLE [dbo].[Customer] CHECK CONSTRAINT [FK_Customer_Person_PersonID]
GO
ALTER TABLE [dbo].[Customer]  WITH CHECK ADD  CONSTRAINT [FK_Customer_SalesTerritory_TerritoryID] FOREIGN KEY([TerritoryID])
REFERENCES [dbo].[SalesTerritory] ([TerritoryID])
GO
ALTER TABLE [dbo].[Customer] CHECK CONSTRAINT [FK_Customer_SalesTerritory_TerritoryID]
GO
ALTER TABLE [dbo].[Intervention]  WITH CHECK ADD  CONSTRAINT [FK_Intervention_Formateur] FOREIGN KEY([TrainerID])
REFERENCES [dbo].[Trainer] ([TrainerID])
GO
ALTER TABLE [dbo].[Intervention] CHECK CONSTRAINT [FK_Intervention_Formateur]
GO
ALTER TABLE [dbo].[Intervention]  WITH CHECK ADD  CONSTRAINT [FK_Intervention_Formation] FOREIGN KEY([TrainingId])
REFERENCES [dbo].[Training] ([TrainingId])
GO
ALTER TABLE [dbo].[Intervention] CHECK CONSTRAINT [FK_Intervention_Formation]
GO
ALTER TABLE [dbo].[OrderDetail]  WITH CHECK ADD  CONSTRAINT [FK_OrderDetail_OrderHeader_OrderID] FOREIGN KEY([OrderID])
REFERENCES [dbo].[OrderHeader] ([SalesOrderID])
ON DELETE CASCADE
GO
ALTER TABLE [dbo].[OrderDetail] CHECK CONSTRAINT [FK_OrderDetail_OrderHeader_OrderID]
GO
ALTER TABLE [dbo].[OrderDetail]  WITH CHECK ADD  CONSTRAINT [FK_OrderDetail_Product] FOREIGN KEY([ProductID])
REFERENCES [dbo].[Product] ([ProductID])
GO
ALTER TABLE [dbo].[OrderDetail] CHECK CONSTRAINT [FK_OrderDetail_Product]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [FK_OrderHeader_Address_BillToAddressID] FOREIGN KEY([BillToAddressID])
REFERENCES [dbo].[Address] ([AddressID])
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [FK_OrderHeader_Address_BillToAddressID]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [FK_OrderHeader_Customer_CustomerID] FOREIGN KEY([CustomerID])
REFERENCES [dbo].[Customer] ([CustomerID])
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [FK_OrderHeader_Customer_CustomerID]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [FK_OrderHeader_SalesPerson_SalesPersonID] FOREIGN KEY([SalesPersonID])
REFERENCES [dbo].[SalesPerson] ([BusinessEntityID])
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [FK_OrderHeader_SalesPerson_SalesPersonID]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [FK_OrderHeader_SalesTerritory_TerritoryID] FOREIGN KEY([TerritoryID])
REFERENCES [dbo].[SalesTerritory] ([TerritoryID])
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [FK_OrderHeader_SalesTerritory_TerritoryID]
GO
ALTER TABLE [dbo].[Path]  WITH NOCHECK ADD  CONSTRAINT [FK_Parcours_Parcours] FOREIGN KEY([ParentPathId])
REFERENCES [dbo].[Path] ([PathID])
GO
ALTER TABLE [dbo].[Path] CHECK CONSTRAINT [FK_Parcours_Parcours]
GO
ALTER TABLE [dbo].[Product]  WITH CHECK ADD  CONSTRAINT [FK_Product_Certification] FOREIGN KEY([CertifID])
REFERENCES [dbo].[Certification] ([CertifID])
GO
ALTER TABLE [dbo].[Product] CHECK CONSTRAINT [FK_Product_Certification]
GO
ALTER TABLE [dbo].[Product]  WITH CHECK ADD  CONSTRAINT [FK_Product_Formation] FOREIGN KEY([TrainingId])
REFERENCES [dbo].[Training] ([TrainingId])
GO
ALTER TABLE [dbo].[Product] CHECK CONSTRAINT [FK_Product_Formation]
GO
ALTER TABLE [dbo].[SalesPerson]  WITH CHECK ADD  CONSTRAINT [FK_SalesPerson_SalesTerritory_TerritoryID] FOREIGN KEY([TerritoryID])
REFERENCES [dbo].[SalesTerritory] ([TerritoryID])
GO
ALTER TABLE [dbo].[SalesPerson] CHECK CONSTRAINT [FK_SalesPerson_SalesTerritory_TerritoryID]
GO
ALTER TABLE [dbo].[SalesTerritory]  WITH CHECK ADD  CONSTRAINT [FK_SalesTerritory_CountryRegion_CountryRegionCode] FOREIGN KEY([CountryRegionCode])
REFERENCES [dbo].[CountryRegion] ([CountryRegionCode])
GO
ALTER TABLE [dbo].[SalesTerritory] CHECK CONSTRAINT [FK_SalesTerritory_CountryRegion_CountryRegionCode]
GO
ALTER TABLE [dbo].[Session]  WITH NOCHECK ADD  CONSTRAINT [FK_PasserTest_Certification] FOREIGN KEY([CertificationID])
REFERENCES [dbo].[Certification] ([CertifID])
GO
ALTER TABLE [dbo].[Session] CHECK CONSTRAINT [FK_PasserTest_Certification]
GO
ALTER TABLE [dbo].[Session]  WITH CHECK ADD  CONSTRAINT [FK_Session_Person1] FOREIGN KEY([PersonID])
REFERENCES [dbo].[Person] ([BusinessEntityID])
GO
ALTER TABLE [dbo].[Session] CHECK CONSTRAINT [FK_Session_Person1]
GO
ALTER TABLE [dbo].[StateProvince]  WITH CHECK ADD  CONSTRAINT [FK_StateProvince_CountryRegion_CountryRegionCode] FOREIGN KEY([CountryRegionCode])
REFERENCES [dbo].[CountryRegion] ([CountryRegionCode])
GO
ALTER TABLE [dbo].[StateProvince] CHECK CONSTRAINT [FK_StateProvince_CountryRegion_CountryRegionCode]
GO
ALTER TABLE [dbo].[StateProvince]  WITH CHECK ADD  CONSTRAINT [FK_StateProvince_SalesTerritory_TerritoryID] FOREIGN KEY([TerritoryID])
REFERENCES [dbo].[SalesTerritory] ([TerritoryID])
GO
ALTER TABLE [dbo].[StateProvince] CHECK CONSTRAINT [FK_StateProvince_SalesTerritory_TerritoryID]
GO
ALTER TABLE [dbo].[OrderDetail]  WITH CHECK ADD  CONSTRAINT [CK_OrderDetail_OrderQty] CHECK  (([OrderQty]>(0)))
GO
ALTER TABLE [dbo].[OrderDetail] CHECK CONSTRAINT [CK_OrderDetail_OrderQty]
GO
ALTER TABLE [dbo].[OrderDetail]  WITH CHECK ADD  CONSTRAINT [CK_OrderDetail_UnitPrice] CHECK  (([UnitPrice]>=(0.00)))
GO
ALTER TABLE [dbo].[OrderDetail] CHECK CONSTRAINT [CK_OrderDetail_UnitPrice]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [CK_OrderHeader_DueDate] CHECK  (([DueDate]>=[OrderDate]))
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [CK_OrderHeader_DueDate]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [CK_OrderHeader_Reduc] CHECK  (([Reduc]>=(0.00)))
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [CK_OrderHeader_Reduc]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [CK_OrderHeader_Status] CHECK  (([Status]>=(0) AND [Status]<=(8)))
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [CK_OrderHeader_Status]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [CK_OrderHeader_SubTotal] CHECK  (([SubTotal]>=(0.00)))
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [CK_OrderHeader_SubTotal]
GO
ALTER TABLE [dbo].[OrderHeader]  WITH CHECK ADD  CONSTRAINT [CK_OrderHeader_TaxAmt] CHECK  (([TaxAmt]>=(0.00)))
GO
ALTER TABLE [dbo].[OrderHeader] CHECK CONSTRAINT [CK_OrderHeader_TaxAmt]
GO
ALTER TABLE [dbo].[Person]  WITH CHECK ADD  CONSTRAINT [CK_Person_PersonType] CHECK  (([PersonType] IS NULL OR (upper([PersonType])='GC' OR upper([PersonType])='SP' OR upper([PersonType])='EM' OR upper([PersonType])='IN' OR upper([PersonType])='VC' OR upper([PersonType])='SC')))
GO
ALTER TABLE [dbo].[Person] CHECK CONSTRAINT [CK_Person_PersonType]
GO
ALTER TABLE [dbo].[SalesPerson]  WITH CHECK ADD  CONSTRAINT [CK_SalesPerson_Bonus] CHECK  (([Bonus]>=(0.00)))
GO
ALTER TABLE [dbo].[SalesPerson] CHECK CONSTRAINT [CK_SalesPerson_Bonus]
GO
ALTER TABLE [dbo].[SalesPerson]  WITH CHECK ADD  CONSTRAINT [CK_SalesPerson_CommissionPct] CHECK  (([CommissionPct]>=(0.00)))
GO
ALTER TABLE [dbo].[SalesPerson] CHECK CONSTRAINT [CK_SalesPerson_CommissionPct]
GO
ALTER TABLE [dbo].[SalesPerson]  WITH CHECK ADD  CONSTRAINT [CK_SalesPerson_SalesLastYear] CHECK  (([SalesLastYear]>=(0.00)))
GO
ALTER TABLE [dbo].[SalesPerson] CHECK CONSTRAINT [CK_SalesPerson_SalesLastYear]
GO
ALTER TABLE [dbo].[SalesPerson]  WITH CHECK ADD  CONSTRAINT [CK_SalesPerson_SalesQuota] CHECK  (([SalesQuota]>(0.00)))
GO
ALTER TABLE [dbo].[SalesPerson] CHECK CONSTRAINT [CK_SalesPerson_SalesQuota]
GO
ALTER TABLE [dbo].[SalesPerson]  WITH CHECK ADD  CONSTRAINT [CK_SalesPerson_SalesYTD] CHECK  (([SalesYTD]>=(0.00)))
GO
ALTER TABLE [dbo].[SalesPerson] CHECK CONSTRAINT [CK_SalesPerson_SalesYTD]
GO
ALTER TABLE [dbo].[SalesTerritory]  WITH CHECK ADD  CONSTRAINT [CK_SalesTerritory_CostLastYear] CHECK  (([CostLastYear]>=(0.00)))
GO
ALTER TABLE [dbo].[SalesTerritory] CHECK CONSTRAINT [CK_SalesTerritory_CostLastYear]
GO
ALTER TABLE [dbo].[SalesTerritory]  WITH CHECK ADD  CONSTRAINT [CK_SalesTerritory_CostYTD] CHECK  (([CostYTD]>=(0.00)))
GO
ALTER TABLE [dbo].[SalesTerritory] CHECK CONSTRAINT [CK_SalesTerritory_CostYTD]
GO
ALTER TABLE [dbo].[SalesTerritory]  WITH CHECK ADD  CONSTRAINT [CK_SalesTerritory_SalesLastYear] CHECK  (([SalesLastYear]>=(0.00)))
GO
ALTER TABLE [dbo].[SalesTerritory] CHECK CONSTRAINT [CK_SalesTerritory_SalesLastYear]
GO
ALTER TABLE [dbo].[SalesTerritory]  WITH CHECK ADD  CONSTRAINT [CK_SalesTerritory_SalesYTD] CHECK  (([SalesYTD]>=(0.00)))
GO
ALTER TABLE [dbo].[SalesTerritory] CHECK CONSTRAINT [CK_SalesTerritory_SalesYTD]
GO
~~~~

___

## **5 - Changelog**

### **- 17/12/2020**
- Table descriptions with relations addded.
- Summary added.

### **- 16/12/2020**
- Wiki created with database model picture, tables declaration scripts and constraint command listed.

### **- 15/12/2020**
The first script given contained franglais syntax and other errors.

Changes :
- Franglais error corrections

~~~~sql
[From] => [To]

[ContenuFormation] => [TrainingContent]
[FormationID] => [TrainingId] 
[DateDeb] => [StaDate] 
[DateFin] => [EndDate] 
[PK_ContenuFormation] => [PK_TrainingContent] 
[Formateur] => [Trainer]
[FormateurID] => [TrainerID] 
[Prenom] => [FirstName] 
[Nom] => [LastName] 
[Adresse] => [Address] 
[Telephone] => [Phone] 
[Mail] => [EMail] 
[PK_Formateur] => [PK_Trainer] 
[Formation] => [Training]
[FormationID] => [TrainingID] 
[PK_Formation] => [PK_Training] 
[PK_PasserTest] => [PK_TestPass]
~~~~

- Cast missing in `Customer` table declaration

~~~~sql
From:
[AccountNumber]  AS (isnull('AW_DIIAGE_'+[CustomerID],'')) 

To:
[AccountNumber]  AS (isnull('AW_DIIAGE_'+CONVERT([varchar](255),[CustomerID]),''))
~~~~