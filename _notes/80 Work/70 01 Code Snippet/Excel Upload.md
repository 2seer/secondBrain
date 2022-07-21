---

title : 엑셀추출

---

# 기능 정의
- 엑셀 추출 -> 추출한 데이터에 데이터를 추가로 입력 후 업로드로 하여 일괄 등록이 가능하게 합니다.
## 엑셀 추출 
### 컨트롤러
```Java
@SneakyThrows  
@GetMapping(value = "/excel", params = "format=xls")  
public String orderExcel(Model model, 
						Req req, 
						HttpServletResponse response) {  
    List<Dto> result = service.getList(req);  
    model.addAttribute("list", result);  
    response.setHeader("Set-Cookie", "fileDownload=true; path=/");  
    response.setHeader("Connection", "Keep-Alive");  
    return "XlsxView";  
}
```

### 서비스
```java
@SneakyThrows  
public List<Dto> getList(Req req) {  
	List<Dto> result = new ArrayList<>();
	
	// 해당 setting 부분을 수정
    // 서비스 가공 전에 컨트롤러에서 했던 부분은 service 로 이동
    // if (req..
	// 쿼리 문 작성
    // 데이터가 있는 부분만 재가공
    return result;
}
```

### 추출 템플릿 `XlsxView`
```Java
@Component("XlsxView")  
public class XlsxView extends AbstractXlsxView {  
    @Override  
    protected void buildExcelDocument(Map<String, Object> map, Workbook workbook, HttpServletRequest req, HttpServletResponse res) throws Exception {  
        res.setHeader("Content-Disposition", "attachment;filename=" + new String("내역".getBytes(StandardCharsets.UTF_8), "8859_1") + ".xlsx");  
        List<Dto> result = (List<Dto>) map.get("list");  
        Sheet sheet = workbook.createSheet("내역");  
        Row header = sheet.createRow(0);  
        String[] cellHeader = {
						        "번호", 
							    "parameter 1",
						        "parameter 2",
						        "parameter 3",
						        "parameter 4",
						        "parameter 5",
						        "parameter 6",
						        "parameter 7",
						        "parameter 8",
						        "date", 
						        "status"
						        };  
						       
        for (int i = 0; i < cellHeader.length; i++) {  
            header.createCell(i).setCellValue(cellHeader[i]);  
        }        
        int rowCount = 1;  
        for (Dto data : result) {  
            Row row = sheet.createRow(rowCount++);  
            int cellIdx = 0;  
            row.createCell(cellIdx++).setCellValue(data.getParameter1());  
            row.createCell(cellIdx++).setCellValue(data.getParameter2());  
            row.createCell(cellIdx++).setCellValue(data.getParameter3().equals("TYPE") ? "parameter1" : "parameter2");  
            row.createCell(cellIdx++).setCellValue(data.getParameter4());  
            row.createCell(cellIdx++).setCellValue(data.getParameter5());  
            row.createCell(cellIdx++).setCellValue(data.getParameter6());  
            row.createCell(cellIdx++).setCellValue(data.getParameter7() != null ? code.getParameter7() : "P");  
            row.createCell(cellIdx++).setCellValue(data.getParameter8() != null ? code.getParameter8() + "P" : "0");  
            row.createCell(cellIdx++).setCellValue(data.getParameter9());  
            row.createCell(cellIdx++).setCellValue(data.getDate() == null ? "" : data.getDate().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));  
            row.createCell(cellIdx).setCellValue(code.getStatus() == null ? "" : data.getStatus().getDescription());  
        }    }}
```

## 엑셀 업로드
ExcelReader.java
```Java
@Component  
public class ExcelReader {
// 첫 row 포함
public <T> List<T> readFileToList(final MultipartFile multipartFile, final Function<Row, T> rowFunc) throws IOException, InvalidFormatException {  
	final Workbook workbook = readWorkbook(multipartFile);  
	final Sheet sheet = workbook.getSheetAt(0);  
	final int rowCount = sheet.getPhysicalNumberOfRows();  
	return IntStream.range(0, rowCount)  
		.mapToObj(rowIndex -> rowFunc.apply(sheet.getRow(rowIndex)))  
        .collect(Collectors.toList());  
}  
  
// 첫 row skip
public <T> List<T> readFileToList1(final MultipartFile multipartFile, final Function<Row, T> rowFunc) throws IOException, InvalidFormatException {  
	final Workbook workbook = readWorkbook(multipartFile);  
	final Sheet sheet = workbook.getSheetAt(0);  
	final int rowCount = sheet.getPhysicalNumberOfRows();  
	return IntStream.range(1, rowCount)
		.mapToObj(rowIndex -> rowFunc.apply(sheet.getRow(rowIndex)))  
        .filter(Objects::nonNull)  
        .collect(Collectors.toList());  
}

private Workbook readWorkbook(MultipartFile multipartFile) throws IOException, InvalidFormatException {  
   verifyFileExtension(multipartFile);
   return multipartFileToWorkbook(multipartFile);  
}

private void verifyFileExtension(MultipartFile multipartFile) throws InvalidFormatException {  
   if (!isExcelExtension(multipartFile.getOriginalFilename())) {  
      throw new InvalidFormatException("This file extension is not verify");  
   }
}

private boolean isExcelExtension(String fileName) {  
   return fileName.endsWith("xls") || fileName.endsWith("xlsx");  
}  
  
private boolean isExcelXls(String fileName) {  
   return fileName.endsWith("xls");  
}  
  
private boolean isExcelXlsx(String fileName) {  
   return fileName.endsWith("xlsx");  
}  
  
private Workbook multipartFileToWorkbook(MultipartFile multipartFile) throws IOException {  
   if (isExcelXls(multipartFile.getOriginalFilename())) {  
      return new HSSFWorkbook(multipartFile.getInputStream());  
   } else {  
      return new XSSFWorkbook(multipartFile.getInputStream());  
   }
}
```

Service
```Java
public List<Dto.Req> readFileToList(MultipartFile multipartFile) throws Exception {  
    return excelReader.readFileToList1(multipartFile, Service::form);  
}

private static Dto.Req form(Row row) {  
    Dto.Req req = new Dto.Req();  
    int i = 0;  
    if (getStringCell(row.getCell(i++)) == null && getIntCell(row.getCell(i++)) == null ) {  
        return null;  
    }    
    i = 0;  
    req.setAccount(getStringCell(row.getCell(i++)));  
    req.setPoint(getIntCell(row.getCell(i++)));  
    return req;  
}
  
private static Integer getIntCell(Cell cell) {  
    String dataString;  
    Integer result = null;  
    try {  
        if (cell != null) {  
            dataString = cell.getStringCellValue();  
            if (!dataString.isEmpty()) {  
                result = Integer.parseInt(dataString);  
            }        
        }    
    } catch (IllegalStateException e) {  
        result = (int) cell.getNumericCellValue();  
    } catch (NumberFormatException ne) {  
        result = null;  
    }    
    return result;  
}  
  
private static String getStringCell(Cell cell) {  
    String result = null;  
    try {  
        if (cell != null) {  
            result = cell.getStringCellValue();  
        }    
    } catch (IllegalStateException e) {  
        result = cell.getNumericCellValue() + "";  
    }    
    return result;  
}

public List<String> checkDuplicateData(List<Dto>.Req> data) {  
    List<String> result = new ArrayList<>();  
    List<String> tmp = new ArrayList<>();  
    for (Dto.Req req : data) {  
        if (tmp.contains(req.getAccount())) {  
            result.add(req.getAccount());  
        } else {  
            tmp.add(req.getAccount());  
        }    
    }    
    return result;  
}  
  
  
public List<AccountPointInfoDto.EarnPointCheck> checkEarnPoints(List<AccountPointInfoDto.EarnPointsReq> checkEarnPoints) {  
    List<AccountPointInfoDto.EarnPointCheck> earnPointsChecks = new ArrayList<AccountPointInfoDto.EarnPointCheck>();  
    int rownum = 2;  
    List<String> checkCols;  
    for (AccountPointInfoDto.EarnPointsReq earnPoints : checkEarnPoints) {  
        checkCols = new ArrayList<>();  
        if (earnPoints.getAccount() == null) {  
            checkCols.add("계정(이메일)");  
        }        
        if (earnPoints.getPoint() == 0) {  
            checkCols.add("포인트");  
        }        
        if (checkCols.size() > 0) {  
            AccountPointInfoDto.EarnPointCheck earnPointCheck = new AccountPointInfoDto.EarnPointCheck();  
            earnPointCheck.setRowNum(rownum);  
            earnPointCheck.setEmptyItem(checkCols);  
            earnPointsChecks.add(earnPointCheck);  
        }        
        rownum++;  
    }    
    return earnPointsChecks;  
}

```

```Java
Controller 추가
@PostMapping("/excel")  
public String excelUpload(@RequestParam("extraFile") MultipartFile extraFile,  
                          HttpServletResponse response, HttpServletRequest request) throws Exception {  
  
    String name = "";  
    List<Dto.req> uploadExcelFile = service.readFileToList(extraFile);  
    HttpSession session = request.getSession();  
    if (uploadExcelFile.size() == 0) {  
        response.getWriter().print("NODATA");  
        return "billing/point/upload";  
    }    List<AccountPointInfoDto.EarnPointCheck> earnPointChecks = earnPointsService.checkEarnPoints(uploadExcelFile);  
    if (earnPointChecks.size() > 0) {  
        response.getWriter().print("MISSING");  
        return "billing/point/upload";  
    }    List<String> duplicateList = earnPointsService.checkDuplicateData(uploadExcelFile);  
    if (duplicateList != null && duplicateList.size() > 0) {  
        response.getWriter().print("DUPLICATE" + duplicateList.size());  
        return "billing/point/upload";  
    }  
    JSONArray jsonArray;  
    jsonArray = new JSONArray();  
    String loginIds = "[";  
    for (int i = 0; i < uploadExcelFile.size(); i++) {  
        loginIds = loginIds + "\"" + uploadExcelFile.get(i).getAccount() + "\"";  
        if (i < uploadExcelFile.size() - 1) loginIds = loginIds + ",";  
    }    loginIds = loginIds + "]";  
  
    Dto.CheckAccountIdList accountCheck = service.getAccountId(loginIds);  
    List<Dto.Info> existStatusMember = accountcheck.getExistMembers();  
    List<Dto.NoExistStatusMember> noExistMember = accountcheck.getNotExistMembers();  
  
    if (noExistMember.size() > 0) {  
        for (int i=0; i< noExistMember.size(); i++) {  
            name = name + noExistMember.get(i).getLoginId();  
            if (i < noExistMember.size() -1) name = name + ",";  
        }  
        response.getWriter().print(name);  
        return "billing/point/upload";  
    }  
    for (int i = 0; i < existStatusMember.size(); i++) {  
        JSONObject jsonObject = new JSONObject();  
        jsonObject.put("account_id", existStatusMember.get(i).getAccountId());  
        jsonObject.put("amount", uploadExcelFile.get(i).getPoint());  
        jsonArray.add(jsonObject);  
    }    
    response.getWriter().print("SUCCESS");  
    session.setAttribute("jsonString", jsonArray);  
    return "folder1/folder2/upload";  
}
```