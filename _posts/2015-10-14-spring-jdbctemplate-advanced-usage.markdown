---
layout: post
title:  "Spring JdbcTemplate advanced usage"
date:   2015-10-14 14:00:00 +0800
categories: [java, jdbcTemplate, spring]
---
1.SimpleJdbcInsert, 可回傳ID:

~~~ java

JdbcTemplate template = SpringContextHolder.getBean("sysJdbcTemplate");
        SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(template)
                .withTableName("fin_payment_schedule_collection_history")
                .usingColumns("CREATED", "SOURCEAMOUNT", "STATUS", "UPDATED", "CreatedBy", "GL_JournalLine_SID", "FinPaymentScheduleDetailSID", "sourceCurrency", "UpdatedBy")
                .usingGeneratedKeyColumns("PaymentScheduleCollectionHistorySID");
        Map<String,Object> paramMap = Maps.newHashMap();
        paramMap.put("CREATED", created);
        paramMap.put("SOURCEAMOUNT", sourceAmount);
        paramMap.put("STATUS", status.ordinal());
        paramMap.put("UPDATED", null);
        paramMap.put("CreatedBy", createdBy.getUserSID());
        paramMap.put("GL_JournalLine_SID", gLine.getJournalLineSID());
        paramMap.put("FinPaymentScheduleDetailSID", scheduleDetail.getSid());
        paramMap.put("sourceCurrency", sourceCurrency.getSid());
        paramMap.put("UpdatedBy", null);
        Integer sid = jdbcInsert.executeAndReturnKey(paramMap).intValue();
        EMF.evictCache(getMyType());
        return findBySID(sid);

~~~

2.update:

~~~ java

JdbcTemplate template = SpringContextHolder.getBean("sysJdbcTemplate");
        int result = template.update("UPDATE gl_journalline SET ("
                + "Amt2 = ?, "
                + "Updated = ?, "
                + "UpdatedBy = ?) "
                + "WHERE GL_JournalLine_SID = ?", amt2, new Date(), loginUser.getUserSID(), journalLineSID);
        EMF.evictCache(GLJournalLine.class, journalLineSID);
        return result;

~~~