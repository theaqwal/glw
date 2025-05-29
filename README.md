-- Understand the distribution of all loss types across all machines
SELECT 
    ROUND(AVG(Loss_Root), 2) as avg_root,
    ROUND(AVG(Loss_Breaks), 2) as avg_breaks,
    ROUND(AVG(Loss_Coat), 2) as avg_coat,
    ROUND(AVG(Loss_Diam), 2) as avg_diam,
    ROUND(AVG(Loss_Equip), 2) as avg_equip,
    ROUND(AVG(Loss_Holes), 2) as avg_holes,
    ROUND(AVG(Loss_Index), 2) as avg_index,
    ROUND(AVG(Loss_Tension), 2) as avg_tension,
    ROUND(AVG(Loss_Unacct), 2) as avg_unacct,
    -- Calculate percentages of total
    ROUND(100.0 * SUM(Loss_Root) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as root_pct,
    ROUND(100.0 * SUM(Loss_Breaks) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as breaks_pct,
    ROUND(100.0 * SUM(Loss_Coat) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as coat_pct,
    ROUND(100.0 * SUM(Loss_Diam) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as diam_pct,
    ROUND(100.0 * SUM(Loss_Equip) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as equip_pct,
    ROUND(100.0 * SUM(Loss_Holes) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as holes_pct,
    ROUND(100.0 * SUM(Loss_Index) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as index_pct,
    ROUND(100.0 * SUM(Loss_Tension) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as tension_pct,
    ROUND(100.0 * SUM(Loss_Unacct) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as unacct_pct
FROM analysis_base;

-- Complete machine profile with all loss types
WITH machine_loss_breakdown AS (
    SELECT 
        Cons_Equipment_Name,
        COUNT(*) as runs,
        ROUND(AVG(Loss_Percentage), 2) as avg_loss_pct,
        ROUND(AVG(Total_Loss_Kms), 2) as avg_total_loss,
        -- Calculate percentage contribution of each loss type
        ROUND(100.0 * SUM(Loss_Root) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as root_pct,
        ROUND(100.0 * SUM(Loss_Breaks) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as breaks_pct,
        ROUND(100.0 * SUM(Loss_Coat) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as coat_pct,
        ROUND(100.0 * SUM(Loss_Diam) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as diam_pct,
        ROUND(100.0 * SUM(Loss_Equip) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as equip_pct,
        ROUND(100.0 * SUM(Loss_Holes) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as holes_pct,
        ROUND(100.0 * SUM(Loss_Index) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as index_pct,
        ROUND(100.0 * SUM(Loss_Tension) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as tension_pct,
        ROUND(100.0 * SUM(Loss_Unacct) / NULLIF(SUM(Total_Loss_Kms), 0), 1) as unacct_pct
    FROM analysis_base
    GROUP BY Cons_Equipment_Name
    HAVING COUNT(*) >= 20
)
SELECT 
    Cons_Equipment_Name,
    runs,
    avg_loss_pct,
    root_pct,
    breaks_pct,
    coat_pct,
    diam_pct,
    equip_pct,
    holes_pct,
    index_pct,
    tension_pct,
    unacct_pct,
    -- Find the actual dominant loss type
    GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) as max_loss_pct,
    CASE 
        WHEN root_pct = GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) THEN 'ROOT'
        WHEN breaks_pct = GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) THEN 'BREAKS'
        WHEN coat_pct = GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) THEN 'COAT'
        WHEN diam_pct = GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) THEN 'DIAM'
        WHEN equip_pct = GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) THEN 'EQUIP'
        WHEN holes_pct = GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) THEN 'HOLES'
        WHEN index_pct = GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) THEN 'INDEX'
        WHEN tension_pct = GREATEST(root_pct, breaks_pct, coat_pct, diam_pct, equip_pct, holes_pct, index_pct, tension_pct, unacct_pct) THEN 'TENSION'
        ELSE 'UNACCT'
    END as dominant_loss_type
FROM machine_loss_breakdown
ORDER BY avg_loss_pct ASC;

-- Simplified view focusing on top loss contributors for each machine
SELECT 
    Cons_Equipment_Name,
    COUNT(*) as runs,
    ROUND(AVG(Loss_Percentage), 2) as avg_loss_pct,
    -- Show only significant loss contributors (>5% of total)
    CASE WHEN 100.0 * SUM(Loss_Root) / NULLIF(SUM(Total_Loss_Kms), 0) > 5 
         THEN ROUND(100.0 * SUM(Loss_Root) / NULLIF(SUM(Total_Loss_Kms), 0), 1) 
         ELSE NULL END as root_pct,
    CASE WHEN 100.0 * SUM(Loss_Breaks) / NULLIF(SUM(Total_Loss_Kms), 0) > 5 
         THEN ROUND(100.0 * SUM(Loss_Breaks) / NULLIF(SUM(Total_Loss_Kms), 0), 1) 
         ELSE NULL END as breaks_pct,
    CASE WHEN 100.0 * SUM(Loss_Coat) / NULLIF(SUM(Total_Loss_Kms), 0) > 5 
         THEN ROUND(100.0 * SUM(Loss_Coat) / NULLIF(SUM(Total_Loss_Kms), 0), 1) 
         ELSE NULL END as coat_pct,
    CASE WHEN 100.0 * SUM(Loss_Diam) / NULLIF(SUM(Total_Loss_Kms), 0) > 5 
         THEN ROUND(100.0 * SUM(Loss_Diam) / NULLIF(SUM(Total_Loss_Kms), 0), 1) 
         ELSE NULL END as diam_pct,
    CASE WHEN 100.0 * SUM(Loss_Unacct) / NULLIF(SUM(Total_Loss_Kms), 0) > 5 
         THEN ROUND(100.0 * SUM(Loss_Unacct) / NULLIF(SUM(Total_Loss_Kms), 0), 1) 
         ELSE NULL END as unacct_pct
FROM analysis_base
GROUP BY Cons_Equipment_Name
HAVING COUNT(*) >= 20
ORDER BY avg_loss_pct ASC;
