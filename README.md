# Codescan Exporter - User Manual & Technical Documentation

## Overview

The **Codescan Exporter** is a Visual Studio Code extension that provides a user-friendly interface to export code quality issues from Codescan servers. It allows developers and quality assurance teams to retrieve, analyze, and export detailed reports of code issues including bugs, vulnerabilities, and code smells.

## User Manual

### Getting Started

1. **Installation**: Install the extension in VS Code
2. **Activation**: Open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and run `CodescanExporter: Open Codescan Exporter`
3. **Interface**: A dedicated webview panel opens with an intuitive form interface

### Configuration Steps

#### 1. Server Connection
- **Server URL**: Enter your Codescan instance URL (default: `https://app-eu.codescan.io`)
- **Security Token**: Provide your authentication token (will be masked for security)
- **Organization Key**: Enter your organization identifier (default: `afklm`)

#### 2. Load Projects
- Click **"📂 Load Projects"** to fetch available projects from your organization
- The system validates your credentials and retrieves all accessible projects
- Projects are automatically sorted alphabetically for easy selection

#### 3. Select Project & Filters
- **Project**: Choose from the dropdown of loaded projects
- **Issue Types** (optional): Select specific types:
  - 🔍 Code Smell
  - 🐛 Bug
  - 🛡️ Vulnerability
- **Severities** (optional): Filter by severity levels:
  - ℹ️ Info
  - 🟡 Minor
  - 🟠 Major
  - 🔴 Critical
  - ⛔ Blocker
- **From Date** (optional): Only include issues created after this date

#### 4. Export Options
- **📊 Export Issues (First Page)**: Quick export of first 500 issues
- **📈 Export All Issues**: Comprehensive export of all matching issues (may take longer)

### Results & Export

#### Summary Dashboard
After export, you'll see a comprehensive summary including:
- Total number of issues found
- Breakdown by severity levels
- Distribution by issue types
- Top 10 components with most issues

#### Export Formats
- **💾 Save as JSON**: Structured data with all issue details
- **📄 Save as CSV**: Spreadsheet-compatible format
- **📋 Copy to Clipboard**: Quick copy for immediate use

#### Data Fields Included
Each exported issue contains:
- Creation and update dates
- Rule information (ID and name)
- Status and severity
- File location and line number
- Issue message and type
- Author information
- Associated tags
- Hotspot indicators

## Technical Highlights

### Architecture Overview

#### 1. **Modular Design**
The extension follows a clean separation of concerns:
- **`codescanExporter.ts`**: Core API client and business logic
- **`extension.ts`**: VS Code integration and webview management
- **HTML/CSS/JS**: Rich user interface within the webview

#### 2. **TypeScript Implementation**
- **Strong Typing**: Comprehensive interfaces for all data structures
- **Type Safety**: Prevents runtime errors with compile-time checks
- **IntelliSense Support**: Enhanced developer experience

#### 3. **API Integration**
```typescript
interface CodescanConfig {
  server: string;
  token: string;
  organizationKey: string;
  projectKey: string;
  type: IssueType[];
  severities: Severity[];
  fromDate: string;
}
```

### Key Technical Features

#### 1. **Robust HTTP Client**
- Built on `node-fetch` for reliable API communication
- Comprehensive error handling with detailed error messages
- Base64 authentication encoding
- Request/response logging for debugging

#### 2. **Pagination Management**
```typescript
public async exportAllIssues(): Promise<Issue[]> {
  const firstPage = await this.exportIssues();
  let allIssues = [...firstPage.issues];
  
  if (firstPage.pages > 1) {
    const promises: Promise<{ issues: Issue[] }>[] = [];
    for (let page = 2; page <= firstPage.pages; page++) {
      promises.push(this.exportIssuesPage(page));
    }
    const additionalPages = await Promise.all(promises);
    // Merge all pages...
  }
}
```

#### 3. **VS Code Integration**
- **Webview Panel**: Rich HTML interface within VS Code
- **Message Passing**: Secure communication between webview and extension
- **Context Retention**: Maintains state when panel is hidden
- **Error Propagation**: Comprehensive error handling and user feedback

#### 4. **Data Processing**
- **Rule Enrichment**: Automatically maps rule IDs to human-readable names
- **Summary Generation**: Real-time statistics calculation
- **File Name Extraction**: Intelligent component path parsing
- **CSV Conversion**: Robust CSV export with quote escaping

#### 5. **Security Features**
- **Token Masking**: Password field for sensitive authentication data
- **Input Validation**: Comprehensive parameter validation
- **Error Sanitization**: Safe error message handling

### Performance Optimizations

#### 1. **Concurrent Processing**
- Parallel API calls for multi-page exports using `Promise.all()`
- Optimized pagination with 500 issues per request

#### 2. **Memory Management**
- Efficient data structures for large datasets
- Stream-like processing for exports
- Garbage collection friendly patterns

#### 3. **User Experience**
- Real-time loading indicators
- Progress messaging during long operations
- Responsive interface design

### Code Quality Features

#### 1. **Error Handling**
```typescript
private async makeRequest<T>(url: string): Promise<T> {
  try {
    const response = await fetch(url, {
      method: "GET",
      headers: this.getAuthHeaders(),
    });
    
    if (!response.ok) {
      const errorText = await response.text();
      throw new Error(`HTTP ${response.status}: ${response.statusText}. ${errorText}`);
    }
    // Process response...
  } catch (error) {
    if (error instanceof Error) {
      throw new Error(`Network request failed: ${error.message}`);
    }
    throw new Error("Unknown network error occurred");
  }
}
```

#### 2. **Configuration Validation**
- Required field validation
- URL format validation
- Data type enforcement

#### 3. **Logging & Debugging**
- Request URL logging
- Response preview logging
- File output for debugging (`output.json`)

### Extension Points

#### 1. **Customizable Defaults**
- Server URL configuration
- Organization key defaults
- Pagination limits

#### 2. **Extensible Filters**
- Issue type filtering
- Severity level filtering
- Date range filtering
- Custom query parameters

#### 3. **Export Formats**
- JSON with structured data
- CSV for spreadsheet analysis
- Clipboard integration
- File system integration

### Browser Compatibility
- Modern JavaScript features
- CSS Grid for responsive layouts
- Fetch API for HTTP requests
- Clipboard API integration

## Usage Scenarios

### Development Teams
- **Code Review**: Export issues for specific components before releases
- **Quality Gates**: Generate reports for continuous integration
- **Technical Debt**: Track code smells and improvements over time

### Quality Assurance
- **Vulnerability Assessment**: Focus on security-related issues
- **Compliance Reporting**: Generate comprehensive quality reports
- **Trend Analysis**: Monitor issue creation and resolution patterns

### Management Reporting
- **Project Health**: High-level overview of code quality metrics
- **Resource Planning**: Identify components requiring attention
- **Progress Tracking**: Monitor quality improvements over time

## Maintenance & Support

### Logging
The extension provides comprehensive logging for troubleshooting:
- API request/response details
- Error messages with context
- Performance metrics for large exports

### Configuration Management
- Settings persist within VS Code workspace
- Easy credential management
- Flexible server configuration

### Extensibility
The modular architecture allows for easy enhancement:
- Additional export formats
- Custom filtering options
- Integration with other quality tools