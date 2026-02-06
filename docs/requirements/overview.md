# Product Requirements Overview

**Document Version:** 1.0
**Last Updated:** 2026-02-02
**Status:** Approved
**Owner:** Product Manager

---

## 1. Executive Summary

### 1.1 Product Vision

Lunar Reminder App la ung dung mobile giup cong dan Viet Nam tu 12 tuoi tro len quan ly va nho cac su kien quan trong theo lich am, dac biet la cac ngay gio, chap, le, sinh nhat, va ngay ky niem. Ung dung tu dong chuyen doi lich am sang duong, gui thong bao thong minh, va giup nguoi dung khong bo lo cac su kien van hoa truyen thong.

### 1.2 Key Features

- **Quan ly su kien theo lich am/duong**: Tao, sua, xoa su kien voi lich am hoac duong
- **Chuyen doi lich thong minh**: Tu dong convert lich am Viet Nam <-> lich duong
- **Thong bao tang dan**: Nhac nho voi tan suat tang khi gan den ngay su kien
- **Phan loai su kien**: Gio/Chap, Sinh nhat, Le hoi, Ngay cuoi, Khac
- **Nhom su kien**: Tao nhom tuy chinh (Gia dinh noi, ngoai, ban be...)
- **Chia se**: Chia se su kien qua link voi nguoi khac
- **Export**: Xuat sang Google Calendar hoac lich thiet bi
- **Widget**: Hien thi su kien sap toi tren home screen
- **Calendar View**: Xem lich am + duong song song
- **Tim kiem**: Tim su kien theo ten, loai
- **Da ngon ngu**: Tieng Viet (chinh), English
- **Giao dien truyen thong VN**: Mau sac, hoa tiet van hoa Viet Nam

### 1.3 Target Platform

- **Mobile App**: iOS (13+) va Android (8+)
- **Tech**: React Native voi custom UI components

### 1.4 Timeline

- **MVP Development**: 1 tuan (7 ngay)
- **Launch Target**: Tuan 2 (sau testing)

---

## 2. Problem Statement

### 2.1 Current Pain Points

**Van de 1: Quen ngay gio, chap**
- Nhieu ban tre va gia dinh khong nho het cac ngay gio theo lich am
- Moi nam lich am chuyen sang lich duong khac nhau -> kho nho
- Khong co cong cu chuyen biet cho lich am Viet Nam

**Van de 2: Lich hien tai khong ho tro lich am**
- Google Calendar, Apple Calendar chi co lich duong
- Phai tu tinh toan hoac tra cuu lich am moi nam
- Khong co thong bao thong minh theo van hoa VN

**Van de 3: Thieu tinh nang phu hop van hoa**
- Cac app nhac nho chung khong hieu ngu canh VN
- Khong phan biet gio (can nhac som) vs sinh nhat
- Khong co tinh nang chia se cho gia dinh

### 2.2 Target Problem

**Lam sao de nguoi Viet Nam, dac biet la the he tre, co the:**
1. De dang quan ly cac su kien theo lich am
2. Nhan thong bao kip thoi de chuan bi
3. Chia se va dong bo voi gia dinh
4. Giu gin cac gia tri van hoa truyen thong

---

## 3. Goals & Objectives

### 3.1 Business Goals

1. **Launch MVP trong 1 tuan** voi core features
2. **Dat 1,000 users** trong thang dau
3. **User retention > 60%** sau 1 thang su dung
4. **Positive feedback** tu community ve van hoa VN

### 3.2 User Goals

1. **Khong bo lo** cac ngay gio, chap quan trong
2. **Tiet kiem thoi gian** tra cuu lich am
3. **De dang chia se** voi gia dinh
4. **Trai nghiem than thien** voi moi lua tuoi

### 3.3 Technical Goals

1. **Performance**: Load time < 2s
2. **Reliability**: Uptime 99%+
3. **Notification**: 100% delivery rate
4. **Offline-first**: Hoat dong khong can internet
5. **Data sync**: Dong bo khi co internet

---

## Related Documentation

- [Target Users & Personas](./personas.md)
- [Use Cases & Scenarios](./use-cases.md)
- [Feature Requirements](./features/)
- [Non-Functional Requirements](./non-functional.md)
- [User Stories](./user-stories.md)
- [Requirements Index](./index.md)
