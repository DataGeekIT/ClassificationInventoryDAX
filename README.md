# Classification Inventory DAX
DAX Function - Classification Stock Inventory to write off slow moving or non moving.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The DAX function "Classification" evaluates various conditions based on the values of columns such as "FY24 Consumption," "Demand," "BZ," "FY24 Receipt," and "FY23 Consumption" across related tables. It categorizes data into different classifications including "A1," "A2," "E1," "E2," "Slow Moving," and "No Moving 2+ years" based on specific criteria. These criteria consider factors such as the presence or absence of values in certain columns, comparisons between column values, and the fulfillment of logical conditions outlined in the business requirements.
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Classification = 
VAR FY24_Consumption = SUMX(RELATEDTABLE('LN INFOR MASTER DATA'), 'LN INFOR MASTER DATA'[FY24 CONSUMPTION])
VAR Demand = SUMX(RELATEDTABLE('SQL Demand'), 'SQL Demand'[qty])
VAR BZ = SUMX(RELATEDTABLE('SQL On Stock'), 'SQL On Stock'[On Hand Q-ty])
VAR FY24_Receipt = SUMX(RELATEDTABLE('LN INFOR MASTER DATA'), 'LN INFOR MASTER DATA'[FY24 RECEIPT])
VAR FY23_Consumption = SUMX(RELATEDTABLE('LN INFOR MASTER DATA'), 'LN INFOR MASTER DATA'[FY23 CONSUMPTION])

RETURN
    IF(
        NOT(ISBLANK(FY24_Consumption)) && ISBLANK(Demand) && BZ <= Demand && BZ < FY24_Receipt && BZ < FY24_Consumption && (NOT(ISBLANK(FY23_Consumption)) || ISBLANK(FY23_Consumption)),
        "A1",
        IF(
            NOT(ISBLANK(FY24_Consumption)) && NOT(ISBLANK(Demand)) && BZ <= Demand && BZ < FY24_Receipt && BZ < FY24_Consumption && (NOT(ISBLANK(FY23_Consumption)) || ISBLANK(FY23_Consumption)),
            "A2",
            IF(
                NOT(ISBLANK(FY24_Consumption)) && ISBLANK(Demand) && (ISBLANK(FY23_Consumption) || NOT(ISBLANK(FY23_Consumption))) && (BZ > Demand || BZ >= FY24_Receipt || BZ >= FY24_Consumption),
                "E1",
                IF(
                    NOT(ISBLANK(FY24_Consumption)) && NOT(ISBLANK(Demand)) && (ISBLANK(FY23_Consumption) || NOT(ISBLANK(FY23_Consumption))) && (BZ > Demand || BZ >= FY24_Receipt || BZ >= FY24_Consumption),
                    "E2",
                    IF(
                        ISBLANK(FY24_Consumption) && ISBLANK(Demand) && NOT(ISBLANK(FY23_Consumption)),
                        "Slow Moving",
                        IF(
                            ISBLANK(FY24_Consumption) && ISBLANK(Demand) && ISBLANK(FY23_Consumption),
                            "No Moving 2+ years",
                            "A1" // Default classification
                        )
                    )
                )
            )
        )
    )
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


![image](https://github.com/DataGeekIT/ClassificationInventoryDAX/assets/101667752/3504e0d2-e703-4a8b-a6d9-0931f9b6dc86)

