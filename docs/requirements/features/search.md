# Search - Tim kiem

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Section:** 7.1.9 - Core Features

---

## FR-SEARCH-001: Tim kiem su kien

**Priority:** Medium

### Search by:
- Ten su kien (fuzzy search)
- Loai su kien (Gio, Sinh nhat...)
- Nhom

### Display:
- Ket qua realtime khi go
- Highlight keyword
- Sap xep theo relevance

---

## Search UI Mockup

### Search Bar
```
+----------------------------------+
| [Search Icon] Tim kiem su kien   |
+----------------------------------+
```

### Search Results
```
+----------------------------------+
| Tim kiem: "gio"                  |
+----------------------------------+
| 3 ket qua                        |
+----------------------------------+
| [Icon] Gio ong noi               |
|        15/3 am lich              |
+----------------------------------+
| [Icon] Gio ba noi                |
|        20/4 am lich              |
+----------------------------------+
| [Icon] Gio ong ngoai             |
|        5/8 am lich               |
+----------------------------------+
```

---

## Search Algorithm

### Fuzzy Search Implementation
- Use Vietnamese-aware text matching
- Handle diacritics (e.g., "gio" matches "gio")
- Support partial matches
- Case-insensitive

### Search Priority
1. Exact name match
2. Name starts with query
3. Name contains query
4. Event type match
5. Group name match

### Performance Requirements
- Results appear as user types
- Max delay: 200ms
- Support search in 1000+ events

---

## Related Documentation

- [Event Management](./events.md)
- [Event Groups](./groups.md)
- [Features Index](../index.md)
