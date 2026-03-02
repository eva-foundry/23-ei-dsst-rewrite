# GitHub Copilot Instructions Template

**Template Version**: 2.1.0  
**Generated**: January 30, 2026, 2:00 PM EST  
**Last Updated**: January 30, 2026  
**Project Type**: {PROJECT_TYPE}  
**Based on**: EVA Professional Component Architecture Standards (from EVA-JP-v1.2 production learnings)

---

## Release Notes

### Version 2.1.0 (January 30, 2026)
**Breaking Changes**:
- Redesigned PART 2 from generic [TODO] format to AI-instructional placeholders
- Added [INSTRUCTION for AI] markers explaining what content to fill and why
- Introduced **[MANDATORY]** vs **[RECOMMENDED]** category markers for prioritization

**New Features**:
- 11 structured placeholder categories based on production analysis (EVA-JP-v1.2, MS-InfoJP)
- Enhanced placeholder syntax with examples: `[TODO: Add X - e.g., value format]`
- Quick Commands Table (action-oriented lookup)
- Azure Resource Inventory (subscription IDs, resource groups)
- Anti-Patterns sections ([x] FORBIDDEN patterns with explanations)
- Success Criteria / Testable Goals
- Performance/Timing Expectations
- Deployment Status & Known Issues tracking

**Improvements**:
- Template now generates AI instruction manual (not documentation)
- Placeholders show correct format/structure (imperative, conditional, reference patterns)
- Backward compatible: existing PART 2 sections remain valid
- Research-based design: 100% coverage of high-frequency patterns (Quick Commands, Environment Config, File Paths)

**Migration Notes**:
- Projects using v2.0.0 template can continue as-is
- New projects should use v2.1.0 structured placeholders
- Apply-Project07-Artifacts.ps1 v1.4.0+ compatible with both versions

### Version 2.0.0 (January 29, 2026)
**Breaking Changes**:
- Transformed from project-specific to reusable template
- Added comprehensive placeholder system for all project-specific values
- Enhanced with anti-patterns prevention and quality gates

**New Features**:
- Template Usage Instructions section
- Anti-Patterns Prevention section
- Emergency Debugging Protocol
- File Organization Requirements
- Quality Gates checklist

**Improvements**:
- Complete PART 1 preservation (universal best practices)
- Structured placeholder guidance in PART 2
- Professional component implementations remain intact
- Enhanced documentation structure

### Version 1.0.0 (January 9, 2026)
**Initial Production Release**:
- Universal best practices (PART 1)
- EVA-JP-v1.2 project-specific patterns (PART 2)
- Professional Component Architecture (DebugArtifactCollector, SessionManager, StructuredErrorHandler, ProfessionalRunner)
- Azure account management patterns
- Workspace housekeeping principles
- Encoding safety standards

---

## Table of Contents

### PART 1: Universal Best Practices
- [Encoding & Script Safety](#critical-encoding--script-safety)
- [Azure Account Management](#critical-azure-account-management)
- [AI Context Management](#ai-context-management-strategy)
- [Azure Services Inventory](#azure-services--capabilities-inventory)
- [Professional Component Architecture](#professional-component-architecture)
  - [DebugArtifactCollector](#implementation-debugartifactcollector)
  - [SessionManager](#implementation-sessionmanager)
  - [StructuredErrorHandler](#implementation-structurederrorhandler)
  - [ProfessionalRunner](#implementation-zero-setup-project-runner)
- [Professional Transformation](#professional-transformation-methodology)
- [Dependency Management](#dependency-management-with-alternatives)
- [Workspace Housekeeping](#workspace-housekeeping-principles)
- [Code Style Standards](#code-style-standards)

### PART 2: {PROJECT_NAME} Project Specific
- [Documentation Guide](#documentation-guide)
- [Architecture Overview](#architecture-overview)
- [Development Workflows](#development-workflows)
- [Project-Specific Automation](#project-specific-automation)
- [Critical Code Patterns](#critical-code-patterns)
- [Testing](#testing)
- [CI/CD Pipeline](#cicd-pipeline)
- [Troubleshooting](#troubleshooting)
- [Performance Optimization](#performance-optimization)

### PART 3: Quality & Safety
- [Anti-Patterns Prevention](#anti-patterns-prevention)
- [File Organization Requirements](#file-organization-requirements)
- [Quality Gates](#quality-gates)
- [Emergency Debugging Protocol](#emergency-debugging-protocol)

### PART 4: Template Usage
- [Template Usage Instructions](#template-usage-instructions)

---

## Quick Reference

**Most Critical Patterns**:
1. **Encoding Safety** - Always use ASCII-only in scripts (prevents UnicodeEncodeError in Windows cp1252)
2. **Azure Account** - Professional account {PROFESSIONAL_EMAIL} required for {ORGANIZATION} resources (configure based on your subscription)
3. **Component Architecture** - DebugArtifactCollector + SessionManager + StructuredErrorHandler + ProfessionalRunner
4. **Session Management** - Checkpoint/resume capability for long-running operations
5. **Evidence Collection** - Screenshots, HTML dumps, network traces at operation boundaries

**Professional Components** (Full Working Implementations):

| Component | Purpose | Key Methods |
|-----------|---------|-------------|
| **DebugArtifactCollector** | Capture HTML/screenshots/traces | `capture_state()`, `set_page()` |
| **SessionManager** | Checkpoint/resume operations | `save_checkpoint()`, `load_latest_checkpoint()` |
| **StructuredErrorHandler** | JSON error logging | `log_error()`, `log_structured_event()` |
| **ProfessionalRunner** | Zero-setup execution | `auto_detect_project_root()`, `validate_pre_flight()` |

**Where to Find Source**:
- Complete working system: `{PROJECT_IMPLEMENTATION_PATH}`
- Best practices reference: `{BEST_PRACTICES_PATH}`
- Framework architecture: `{FRAMEWORK_ARCHITECTURE_PATH}`

---

## PART 1: UNIVERSAL BEST PRACTICES

> **Applicable to any project, any scenario**  
> Critical patterns, Azure inventory management, workspace organization principles

### Critical: Encoding & Script Safety

**ABSOLUTE BAN: No Unicode/Emojis Anywhere**
- **NEVER use in code**: Checkmarks, X marks, emojis, Unicode symbols, ellipsis
- **NEVER use in reports**: Unicode decorations, fancy bullets, special characters
- **NEVER use in documentation**: Unless explicitly required by specification
- **ALWAYS use**: Pure ASCII - "[PASS]", "[FAIL]", "[ERROR]", "[INFO]", "[WARN]", "..."
- **Reason**: Enterprise Windows cp1252 encoding causes silent UnicodeEncodeError crashes
- **Solution**: Set `PYTHONIOENCODING=utf-8` in batch files as safety measure

**Examples**:
```python
# [FORBIDDEN] Will crash in enterprise Windows
print(" Success")  # Unicode checkmark - NEVER
print("[x] Failed")   # Unicode X - NEVER
print(" Wait...")    # Unicode symbols - NEVER

# [REQUIRED] ASCII-only alternatives
print("[PASS] Success")
print("[FAIL] Failed")
print("[INFO] Wait...")
```

### Critical: Azure Account Management

**Multiple Azure Accounts Pattern**
- **Personal Account**: {PERSONAL_SUBSCRIPTION_NAME} ({PERSONAL_SUBSCRIPTION_ID}) - personal sandbox
- **Professional Account**: {PROFESSIONAL_EMAIL} - {ORGANIZATION} production access
- **{ORGANIZATION} Subscriptions** (require professional account):
  - {DEV_SUBSCRIPTION_NAME} ({DEV_SUBSCRIPTION_ID}) - Dev+Stage environments
  - {PROD_SUBSCRIPTION_NAME} ({PROD_SUBSCRIPTION_ID}) - Production environments

**When Azure CLI fails with "subscription doesn't exist"**:
1. Check current account: `az account show --query user.name`
2. Switch accounts: `az logout` then `az login --use-device-code --tenant {TENANT_ID}`
3. Authenticate with professional email: {PROFESSIONAL_EMAIL}
4. Verify access: `az account list --query "[?contains(id, '{DEV_SUBSCRIPTION_ID_PARTIAL}') || contains(id, '{PROD_SUBSCRIPTION_ID_PARTIAL}')]"`

**Pattern**: If accessing {ORGANIZATION} resources, ALWAYS use professional account

### AI Context Management Strategy

**Pattern**: Systematic approach to avoid context overload

**5-Step Process**:
1. **Assess**: What context do I need? (Don't load everything)
2. **Prioritize**: What's most relevant NOW? (Focus on current task)
3. **Load**: Get specific context only (Use targeted file reads, grep searches)
4. **Execute**: Perform task with loaded context
5. **Verify**: Validate result matches intent

**Example**:
```python
# [AVOID] Bad: Load entire file when only need one function
with open('large_file.py') as f:
    content = f.read()  # Loads 10,000 lines

# [RECOMMENDED] Good: Targeted context loading
grep_search(query="def target_function", includePattern="large_file.py")
read_file(filePath="large_file.py", startLine=450, endLine=500)
```

**When to re-assess context**:
- Task scope changes
- Error requires different context
- User provides new information

### Azure Services & Capabilities Inventory

**Azure OpenAI**
- **Models**: GPT-4, GPT-4 Turbo, text-embedding-ada-002
- **Endpoints**: {AZURE_OPENAI_ENDPOINT}
- **Use Cases**: Chat completions, embeddings, content generation
- **Authentication**: API key or DefaultAzureCredential

**Azure AI Services (Cognitive Services)**
- **Capabilities**: Query optimization, content safety, content understanding
- **Use Cases**: Text analysis, translation, content moderation
- **Pattern**: Always implement fallback for private endpoint failures

**Azure Cognitive Search**
- **Capabilities**: Hybrid search (vector + keyword), semantic ranking
- **Use Cases**: Document search, RAG systems, knowledge bases
- **Pattern**: Use index-based access, implement retry logic

**Azure Cosmos DB**
- **Capabilities**: NoSQL database, session storage, change feed
- **Use Cases**: Session management, audit logs, CDC patterns
- **Pattern**: Use partition keys effectively, implement TTL

**Azure Blob Storage**
- **Capabilities**: Object storage, containers, metadata
- **Use Cases**: Document storage, file uploads, static assets
- **Pattern**: Use managed identity, implement lifecycle policies

**Azure Functions**
- **Capabilities**: Serverless compute, event-driven processing
- **Use Cases**: Document pipelines, webhook handlers, scheduled jobs
- **Pattern**: Use blob triggers, queue bindings

**Azure Document Intelligence**
- **Capabilities**: OCR, form recognition, layout analysis
- **Use Cases**: PDF processing, document extraction
- **Pattern**: Handle rate limits, implement retry logic

### Professional Component Architecture

**Pattern**: Enterprise-grade component design (from Project 06/07)

**Every professional component implements**:
- **DebugArtifactCollector**: Evidence at operation boundaries
- **SessionManager**: Checkpoint/resume capabilities
- **StructuredErrorHandler**: JSON logging with context
- **Observability Wrapper**: Pre-state, execution, post-state capture

**Usage Pattern - Combining Components**:

> **Note**: The following shows a conceptual pattern for combining components. For complete, production-ready implementations you can copy-paste directly, see the detailed sections below.
```python
from pathlib import Path
from datetime import datetime
import json

class ProfessionalComponent:
    """Base class for enterprise-grade components"""
    
    def __init__(self, component_name: str, base_path: Path):
        self.component_name = component_name
        self.base_path = base_path
        
        # Core professional infrastructure
        self.debug_collector = DebugArtifactCollector(component_name, base_path)
        self.session_manager = SessionManager(component_name, base_path)
        self.error_handler = StructuredErrorHandler(component_name, base_path)
    
    async def execute_with_observability(self, operation_name: str, operation):
        """Execute operation with full evidence collection"""
        # 1. ALWAYS capture pre-state
        await self.debug_collector.capture_state(f"{operation_name}_before")
        await self.session_manager.save_checkpoint("before_operation", {
            "operation": operation_name,
            "timestamp": datetime.now().isoformat()
        })
        
        try:
            # 2. Execute operation
            result = await operation()
            
            # 3. ALWAYS capture success state
            await self.debug_collector.capture_state(f"{operation_name}_success")
            await self.session_manager.save_checkpoint("operation_success", {
                "operation": operation_name,
                "result_preview": str(result)[:200]
            })
            return result
            
        except Exception as e:
            # 4. ALWAYS capture error state
            await self.debug_collector.capture_state(f"{operation_name}_error")
            await self.error_handler.log_structured_error(operation_name, e)
            raise
```

**When to use**: Any component that interacts with external systems, complex logic, or enterprise automation

---

### Implementation: DebugArtifactCollector

**Purpose**: Capture comprehensive diagnostic state at system boundaries for rapid debugging

**Working Implementation** (from Project 06):
```python
from pathlib import Path
from datetime import datetime
import json
import asyncio

class DebugArtifactCollector:
    """Systematic evidence capture at operation boundaries
    
    Captures HTML, screenshots, network traces, and structured logs
    for every significant operation to enable rapid debugging.
    """
    
    def __init__(self, component_name: str, base_path: Path):
        """Initialize collector for specific component
        
        Args:
            component_name: Name of component (e.g., "authentication", "data_extraction")
            base_path: Project root directory
        """
        self.component_name = component_name
        self.debug_dir = base_path / "debug" / component_name
        self.debug_dir.mkdir(parents=True, exist_ok=True)
        
        self.page = None  # Set by browser automation
        self.network_log = []  # Populated by network listener
    
    async def capture_state(self, context: str):
        """Capture complete diagnostic state
        
        Args:
            context: Operation context (e.g., "before_login", "after_submit", "error_state")
        """
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        
        # 1. Capture HTML snapshot
        if self.page:
            html_file = self.debug_dir / f"{context}_{timestamp}.html"
            html_content = await self.page.content()
            html_file.write_text(html_content, encoding='utf-8')
        
        # 2. Capture screenshot
        if self.page:
            screenshot_file = self.debug_dir / f"{context}_{timestamp}.png"
            await self.page.screenshot(path=str(screenshot_file), full_page=True)
        
        # 3. Capture network trace
        if self.network_log:
            network_file = self.debug_dir / f"{context}_{timestamp}_network.json"
            network_file.write_text(json.dumps(self.network_log, indent=2))
        
        # 4. Capture structured log with application state
        log_file = self.debug_dir / f"{context}_{timestamp}.json"
        log_file.write_text(json.dumps({
            "timestamp": timestamp,
            "context": context,
            "component": self.component_name,
            "url": self.page.url if self.page else None,
            "viewport": await self.page.viewport_size() if self.page else None
        }, indent=2))
        
        return {
            "html": str(html_file) if self.page else None,
            "screenshot": str(screenshot_file) if self.page else None,
            "network": str(network_file) if self.network_log else None,
            "log": str(log_file)
        }
    
    def set_page(self, page):
        """Attach to browser page for capture"""
        self.page = page
        
        # Enable network logging
        async def log_request(request):
            self.network_log.append({
                "timestamp": datetime.now().isoformat(),
                "type": "request",
                "url": request.url,
                "method": request.method
            })
        
        page.on("request", lambda req: asyncio.create_task(log_request(req)))
```

**Usage Pattern**:
```python
# In your automation component
collector = DebugArtifactCollector("my_component", project_root)
collector.set_page(page)

# Before risky operation
await collector.capture_state("before_submit")

try:
    await risky_operation()
    await collector.capture_state("success")
except Exception as e:
    await collector.capture_state("error")
    raise
```

---

### Implementation: SessionManager

**Purpose**: Enable checkpoint/resume capabilities for long-running operations

**Working Implementation** (from Project 06):
```python
from pathlib import Path
from datetime import datetime, timedelta
import json
import shutil
from typing import Dict, Optional

class SessionManager:
    """Manages persistent session state for checkpoint/resume operations
    
    Enables long-running automation to save progress and resume
    from last successful checkpoint if interrupted.
    """
    
    def __init__(self, component_name: str, base_path: Path):
        """Initialize session manager
        
        Args:
            component_name: Component identifier
            base_path: Project root directory
        """
        self.component_name = component_name
        self.session_dir = base_path / "sessions" / component_name
        self.session_dir.mkdir(parents=True, exist_ok=True)
        
        self.session_file = self.session_dir / "session_state.json"
        self.checkpoint_dir = self.session_dir / "checkpoints"
        self.checkpoint_dir.mkdir(exist_ok=True)
    
    def save_checkpoint(self, checkpoint_id: str, data: Dict) -> Path:
        """Save checkpoint with state data
        
        Args:
            checkpoint_id: Unique checkpoint identifier (e.g., "item_5_processed")
            data: State data to persist
            
        Returns:
            Path to saved checkpoint file
        """
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        checkpoint_file = self.checkpoint_dir / f"{checkpoint_id}_{timestamp}.json"
        
        checkpoint_data = {
            "checkpoint_id": checkpoint_id,
            "timestamp": datetime.now().isoformat(),
            "component": self.component_name,
            "data": data
        }
        
        checkpoint_file.write_text(json.dumps(checkpoint_data, indent=2))
        
        # Update session state to point to latest checkpoint
        self.update_session_state(checkpoint_id, str(checkpoint_file))
        
        return checkpoint_file
    
    def load_latest_checkpoint(self) -> Optional[Dict]:
        """Load most recent checkpoint if available
        
        Returns:
            Checkpoint data or None if no checkpoint exists
        """
        if not self.session_file.exists():
            return None
        
        try:
            session_data = json.loads(self.session_file.read_text())
            checkpoint_file = Path(session_data.get("latest_checkpoint"))
            
            if checkpoint_file.exists():
                return json.loads(checkpoint_file.read_text())
            
        except Exception as e:
            print(f"[WARN] Failed to load checkpoint: {e}")
        
        return None
    
    def update_session_state(self, checkpoint_id: str, checkpoint_path: str):
        """Update session state with latest checkpoint reference"""
        session_data = {
            "component": self.component_name,
            "last_updated": datetime.now().isoformat(),
            "latest_checkpoint": checkpoint_path,
            "checkpoint_id": checkpoint_id
        }
        
        self.session_file.write_text(json.dumps(session_data, indent=2))
    
    def clear_session(self):
        """Clear all session state and checkpoints"""
        if self.checkpoint_dir.exists():
            shutil.rmtree(self.checkpoint_dir)
            self.checkpoint_dir.mkdir()
        
        if self.session_file.exists():
            self.session_file.unlink()
```

**Usage Pattern**:
```python
# Initialize session manager
session_mgr = SessionManager("batch_processor", project_root)

# Try to resume from checkpoint
checkpoint = session_mgr.load_latest_checkpoint()
if checkpoint:
    start_index = checkpoint["data"]["last_processed_index"]
    print(f"[INFO] Resuming from checkpoint: item {start_index}")
else:
    start_index = 0

# Process items with checkpoints
for i in range(start_index, len(items)):
    process_item(items[i])
    
    # Save checkpoint every 10 items
    if i % 10 == 0:
        session_mgr.save_checkpoint(f"item_{i}", {
            "last_processed_index": i,
            "items_completed": i + 1,
            "timestamp": datetime.now().isoformat()
        })
```

---

### Implementation: StructuredErrorHandler

**Purpose**: Provide JSON-based error logging with full context for debugging

**Working Implementation** (from Project 06):
```python
from datetime import datetime
from typing import Dict, Any, Optional
from pathlib import Path
import json
import traceback

class StructuredErrorHandler:
    """Enterprise-grade error handling with structured logging
    
    Captures errors with full context in JSON format for easy parsing
    and analysis. All output is ASCII-safe for enterprise Windows.
    """
    
    def __init__(self, component_name: str, base_path: Path):
        """Initialize error handler
        
        Args:
            component_name: Component identifier
            base_path: Project root directory
        """
        self.component_name = component_name
        self.error_dir = base_path / "logs" / "errors"
        self.error_dir.mkdir(parents=True, exist_ok=True)
    
    def log_error(self, error: Exception, context: Optional[Dict[str, Any]] = None) -> Dict:
        """Log error with structured context
        
        Args:
            error: Exception object
            context: Additional context (operation name, parameters, etc.)
            
        Returns:
            Error report dictionary
        """
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        
        error_report = {
            "timestamp": datetime.now().isoformat(),
            "component": self.component_name,
            "error_type": type(error).__name__,
            "error_message": str(error),
            "traceback": traceback.format_exc(),
            "context": context or {}
        }
        
        # Save to timestamped file
        error_file = self.error_dir / f"{self.component_name}_error_{timestamp}.json"
        error_file.write_text(json.dumps(error_report, indent=2))
        
        # Print ASCII-safe error message
        print(f"[ERROR] {self.component_name}: {type(error).__name__}")
        print(f"[ERROR] Message: {str(error)}")
        print(f"[ERROR] Details saved to: {error_file}")
        
        return error_report
    
    def log_structured_event(self, event_type: str, data: Dict[str, Any]):
        """Log structured event (non-error)
        
        Args:
            event_type: Event type (e.g., "operation_start", "data_validated")
            data: Event data
        """
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        
        event_report = {
            "timestamp": datetime.now().isoformat(),
            "component": self.component_name,
            "event_type": event_type,
            "data": data
        }
        
        # Save to events log
        event_file = self.error_dir.parent / f"{self.component_name}_events_{timestamp}.json"
        event_file.write_text(json.dumps(event_report, indent=2))

class ProjectBaseException(Exception):
    """Base exception with structured error reporting
    
    All custom exceptions should inherit from this to ensure
    consistent error handling and reporting.
    """
    
    def __init__(self, message: str, context: Optional[Dict[str, Any]] = None):
        """Initialize exception with context
        
        Args:
            message: Error description (ASCII only)
            context: Additional error context
        """
        super().__init__(message)
        self.message = message
        self.context = context or {}
        self.timestamp = datetime.now().isoformat()
    
    def get_error_report(self) -> Dict[str, Any]:
        """Generate structured error report
        
        Returns:
            Dictionary with full error details
        """
        return {
            "error_type": self.__class__.__name__,
            "message": self.message,
            "context": self.context,
            "timestamp": self.timestamp
        }
```

**Usage Pattern**:
```python
# Initialize error handler
error_handler = StructuredErrorHandler("my_automation", project_root)

try:
    risky_operation()
except Exception as e:
    # Log with context
    error_handler.log_error(e, context={
        "operation": "data_processing",
        "input_file": "data.csv",
        "current_row": 42
    })
    raise

# Custom exception with automatic context
class DataValidationError(ProjectBaseException):
    pass

try:
    if not is_valid(data):
        raise DataValidationError(
            "Invalid data format",
            context={"expected": "CSV", "received": "JSON"}
        )
except DataValidationError as e:
    error_report = e.get_error_report()
    error_handler.log_error(e, context=error_report["context"])
```

---

### Implementation: Zero-Setup Project Runner

**Purpose**: Enable users to run project from anywhere without configuration

**Working Implementation** (from Project 06):
```python
#!/usr/bin/env python3
"""Professional project runner with zero-setup execution"""

import os
import sys
import argparse
from pathlib import Path
import subprocess
from typing import List

# Set UTF-8 encoding for Windows
os.environ.setdefault('PYTHONIOENCODING', 'utf-8')

class ProfessionalRunner:
    """Zero-setup professional automation wrapper"""
    
    def __init__(self):
        self.project_root = self.auto_detect_project_root()
        self.main_script = "scripts/main_automation.py"
    
    def auto_detect_project_root(self) -> Path:
        """Find project root from any subdirectory
        
        Searches for project markers in current and parent directories
        to enable running from any location within the project.
        """
        current = Path.cwd()
        
        # Project indicators (customize for your project)
        indicators = [
            "scripts/main_automation.py",
            "ACCEPTANCE.md",
            "README.md",
            ".git"
        ]
        
        # Check current directory
        for indicator in indicators:
            if (current / indicator).exists():
                return current
        
        # Check parent directories
        for parent in current.parents:
            for indicator in indicators:
                if (parent / indicator).exists():
                    return parent
        
        # Fallback to current directory
        print("[WARN] Could not auto-detect project root, using current directory")
        return current
    
    def validate_pre_flight(self) -> tuple[bool, str]:
        """Pre-flight checks before execution
        
        Validates environment, dependencies, and project structure.
        
        Returns:
            (success: bool, message: str)
        """
        checks = []
        
        # Check main script exists
        main_script_path = self.project_root / self.main_script
        if not main_script_path.exists():
            return False, f"[FAIL] Main script not found: {main_script_path}"
        checks.append("[PASS] Main script found")
        
        # Check required directories
        required_dirs = ["input", "output", "logs"]
        for dir_name in required_dirs:
            dir_path = self.project_root / dir_name
            if not dir_path.exists():
                dir_path.mkdir(parents=True)
                checks.append(f"[INFO] Created directory: {dir_name}")
            else:
                checks.append(f"[PASS] Directory exists: {dir_name}")
        
        # Check Python dependencies
        try:
            import pandas
            import asyncio
            checks.append("[PASS] Required Python modules available")
        except ImportError as e:
            return False, f"[FAIL] Missing Python module: {e}"
        
        return True, "\n".join(checks)
    
    def build_command(self, **kwargs) -> List[str]:
        """Build command with normalized parameters
        
        Converts user inputs to proper command structure.
        """
        cmd = [
            sys.executable,
            str(self.project_root / self.main_script)
        ]
        
        # Add parameters (customize for your project)
        for key, value in kwargs.items():
            if value is not None:
                if isinstance(value, bool):
                    if value:
                        cmd.append(f"--{key}")
                else:
                    cmd.extend([f"--{key}", str(value)])
        
        return cmd
    
    def execute_with_enterprise_safety(self, cmd: List[str]) -> int:
        """Execute with proper encoding and error handling"""
        # Set environment
        env = os.environ.copy()
        env['PYTHONIOENCODING'] = 'utf-8'
        
        # Change to project root
        original_cwd = os.getcwd()
        os.chdir(self.project_root)
        
        try:
            print(f"[INFO] Project root: {self.project_root}")
            print(f"[INFO] Command: {' '.join(cmd)}")
            print("-" * 60)
            
            result = subprocess.run(cmd, env=env)
            return result.returncode
            
        finally:
            os.chdir(original_cwd)
    
    def run(self, **kwargs) -> int:
        """Main execution entry point"""
        print("[INFO] Professional Runner - Zero-Setup Execution")
        print(f"[INFO] Detected project root: {self.project_root}")
        
        # Pre-flight validation
        success, message = self.validate_pre_flight()
        print("\n" + message)
        
        if not success:
            print("\n[FAIL] Pre-flight checks failed")
            return 1
        
        # Build and execute command
        cmd = self.build_command(**kwargs)
        return self.execute_with_enterprise_safety(cmd)

def main():
    """CLI entry point"""
    parser = argparse.ArgumentParser(
        description="Professional automation runner with zero-setup execution"
    )
    
    # Add your project-specific arguments here
    parser.add_argument("--input", help="Input file path")
    parser.add_argument("--output", help="Output file path")
    parser.add_argument("--headless", action="store_true", help="Run in headless mode")
    
    args = parser.parse_args()
    
    # Create runner and execute
    runner = ProfessionalRunner()
    sys.exit(runner.run(**vars(args)))

if __name__ == "__main__":
    main()
```

**Usage**:
```bash
# Run from anywhere in the project
python run_project.py --input data.csv --output results.csv

# Or create Windows batch wrapper
# run_project.bat:
@echo off
set PYTHONIOENCODING=utf-8
python run_project.py %*
```

---

### Professional Transformation Methodology

**Pattern**: Systematic 4-step approach to enterprise-grade development

**When refactoring or creating automation systems**:

1. **Foundation Systems** (20% of work)
   - Create `debug/`, `evidence/`, `logs/` directory structure
   - Establish coding standards and utilities
   - Implement ASCII-only error handling
   - Set up structured logging infrastructure

2. **Testing Framework** (30% of work)
   - Automated validation with evidence collection
   - Component-level unit tests
   - Integration tests with retry logic
   - Acceptance criteria validation

3. **Main System Refactoring** (40% of work)
   - Apply professional component architecture
   - Integrate validation and observability
   - Implement graceful error handling
   - Add session management and checkpoints

4. **Documentation & Cleanup** (10% of work)
   - Consolidate redundant code
   - Document patterns and decisions
   - Create runbooks and troubleshooting guides
   - Archive superseded implementations

**Quality Gate**: Each phase produces evidence before proceeding to next phase

### Dependency Management with Alternatives

**Pattern**: Handle blocked packages in enterprise environments

**Always provide fallback alternatives**:

```python
# Pattern 1: Try primary, fall back to alternative
try:
    from playwright.async_api import async_playwright
    BROWSER_ENGINE = "playwright"
except ImportError:
    print("[INFO] Playwright not available, using Selenium")
    from selenium import webdriver
    BROWSER_ENGINE = "selenium"

# Pattern 2: Feature detection
def get_available_http_client():
    """Return best available HTTP client"""
    if importlib.util.find_spec("httpx"):
        import httpx
        return httpx.AsyncClient()
    elif importlib.util.find_spec("aiohttp"):
        import aiohttp
        return aiohttp.ClientSession()
    else:
        import urllib.request
        return urllib.request  # Fallback to stdlib

# Pattern 3: Document alternatives in requirements
# requirements.txt:
# playwright>=1.40.0  # Primary choice
# selenium>=4.15.0    # Alternative if playwright blocked
# requests>=2.31.0    # Fallback for basic HTTP
```

**Document why alternatives chosen**: Add comments explaining enterprise constraints

### Workspace Housekeeping Principles

**Context Engineering - Keep AI context clean and focused**

**Best Practices**:
- **Root directory**: Active operations only (`RESTART_SERVERS.ps1`, `README.md`)
- **Context folder**: Use `docs/eva-foundry/` as AI agent "brain"
  - `projects/` - Active work with debugging artifacts
  - `workspace-notes/` - Ephemeral notes, workflow docs
  - `system-analysis/` - Architecture docs, inventory reports
  - `comparison-reports/` - Automated comparison outputs
  - `automation/` - Code generation scripts

**Pattern**: If referenced in copilot-instructions.md or used for AI context -> belongs in `docs/eva-foundry/`

**File Organization Rules**:
1. **Logs** -> `logs/{category}/`
   - `logs/deployment/terraform/` - Terraform logs
   - `logs/deployment/` - Deployment logs
   - `logs/tests/` - Test output

2. **Scripts** -> `scripts/{category}/`
   - `scripts/deployment/` - Deploy, build, infrastructure
   - `scripts/testing/` - Test runners, evidence capture
   - `scripts/setup/` - Installation, configuration
   - `scripts/diagnostics/` - Health checks, validation
   - `scripts/housekeeping/` - Workspace organization

3. **Documentation** -> `docs/{category}/`
   - Implementation docs -> `docs/eva-foundry/projects/{project-name}/`
   - Deployment guides -> `docs/deployment/`
   - Debug sessions -> `docs/eva-foundry/projects/{session-name}-debug/`

**Naming Conventions**:
- **Scripts**: `verb-noun.ps1` (lowercase-with-dashes)
  - [RECOMMENDED] Good: `deploy-infrastructure.ps1`, `test-environment.ps1`
  - [AVOID] Bad: `Deploy-MSInfo-Fixed.ps1`, `TEST_COMPLETE.ps1`
- **Docs**: `CATEGORY-DESCRIPTION.md` (UPPERCASE for status docs)
  - [RECOMMENDED] Good: `DEPLOYMENT-STATUS.md`, `IMPLEMENTATION-SUMMARY.md`
  - [AVOID] Bad: `Final-Status-Report.ps1.md`
- **Logs**: `{operation}-{timestamp}.log` or `{component}.log`

**Self-Organizing Rules for AI Agents**:
- **Before creating a file**: Check if similar file exists in `docs/eva-foundry/`
- **When debugging**: Create session folder `docs/eva-foundry/projects/{issue-name}-debug/`
- **After completing work**: Summarize findings in `docs/eva-foundry/workspace-notes/`
- **When context grows**: Create comparison report, archive superseded versions

**Housekeeping Automation**:
```powershell
# Daily cleanup
.\scripts\housekeeping\organize-workspace.ps1

# Weekly archival
.\scripts\housekeeping\archive-debug-sessions.ps1
```

### Evidence Collection at Operation Boundaries

**Goal**: Systematic evidence capture for rapid debugging

**MANDATORY: Every component operation must capture**:
- **Pre-state**: HTML, screenshots, network traces BEFORE execution
- **Success state**: Evidence on successful completion  
- **Error state**: Full diagnostic artifacts on failure
- **Structured logging**: JSON-based error context with timestamps

**Implementation Pattern**:
```python
class DebugArtifactCollector:
    def __init__(self, component_name: str, base_path: Path):
        self.component_name = component_name
        self.debug_dir = base_path / "debug" / component_name
        self.debug_dir.mkdir(parents=True, exist_ok=True)
    
    async def capture_state(self, context: str):
        """Capture complete diagnostic state"""
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        
        # Capture HTML snapshot
        if self.page:  # Browser automation
            html_file = self.debug_dir / f"{context}_{timestamp}.html"
            await self.page.content().write(html_file)
        
        # Capture screenshot
        if self.page:
            screenshot_file = self.debug_dir / f"{context}_{timestamp}.png"
            await self.page.screenshot(path=screenshot_file)
        
        # Capture network trace
        if self.network_log:
            network_file = self.debug_dir / f"{context}_{timestamp}_network.json"
            network_file.write_text(json.dumps(self.network_log, indent=2))
        
        # Capture structured log
        log_file = self.debug_dir / f"{context}_{timestamp}.json"
        log_file.write_text(json.dumps({
            "timestamp": timestamp,
            "context": context,
            "component": self.component_name,
            "url": str(self.page.url) if self.page else None,
            "state": await self._capture_application_state()
        }, indent=2))
```

**Timestamped Naming Convention (MANDATORY)**:
- Pattern: `{component}_{context}_{YYYYMMDD_HHMMSS}.{ext}`
- Examples:
  - `{component}_debug_error_attempt_1_{YYYYMMDD_HHMMSS}.html`
  - `automation_execution_{YYYYMMDD_HHMMSS}.log`
  - `results_batch_001_{YYYYMMDD_HHMMSS}.csv`
- Benefits: Chronological sorting, parallel execution support, audit trails

**Azure Configuration State Tracking**:

**Structure**:
```
docs/eva-foundry/system-analysis/inventory/.eva-cache/
  azure-connectivity-{subscription}-{timestamp}.md
  azure-permissions-{subscription}-{timestamp}.md
  evidence-{subscription}-{timestamp}.md
  canonical-analysis/{storage-account}-{timestamp}.md
```

**Usage Pattern**:
1. Capture current state: `evidence-{subscription}-{timestamp}.md`
2. Compare with previous state: `evidence-{subscription}-{earlier-timestamp}.md`
3. Generate comparison report: `comparison-report-{timestamp}.md`
4. Document findings in project debug folder

### Code Style Standards

**Python**:
- **Type Hints**: Use for all function signatures
- **Async/Await**: Use throughout for I/O operations
- **Naming**: `snake_case` functions/variables, `PascalCase` classes
- **Formatting**: Black (line length 180) + isort
- **Error Handling**: Wrap external calls with try/except, respect `OPTIONAL` flags

**TypeScript**:
- **Naming**: `camelCase` functions/variables, `PascalCase` components/types
- **Components**: Functional components with hooks
- **State**: React Context for global state
- **Styling**: CSS Modules + component libraries

**Files**:
- Python: `snake_case.py`
- TypeScript: `lowercase-with-dashes.tsx`

---

**You are now ready for project-specific patterns**  
See [PART 2: {PROJECT_NAME} Project Specific](#part-2-project-name-project-specific) below for AI-instructional project patterns.

---



## PART 2: EI-DSST REWRITE PROJECT SPECIFIC

### Project Lock

This file is the copilot-instructions for **23-ei-dsst-rewrite** (23-ei-dsst-rewrite).

The workspace-level bootstrap rule "Step 1 -- Identify the active project from the currently open file path"
applies **only at the initial load of this file** (first read at session start).
Once this file has been loaded, the active project is locked to **23-ei-dsst-rewrite** for the entire session.
Do NOT re-evaluate project identity from editorContext or terminal CWD on each subsequent request.
Work state and sprint context are read from `STATUS.md` and `PLAN.md` at bootstrap -- not from this file.

---

> **Oracle Forms Modernization - API + React Migration**  
> Phase 1: Employee Maintenance Module (Proof of Concept)  
> Customized from Project 07 Foundation Layer on 2026-01-30

### Documentation Guide

**Primary References**:
- **This file** (copilot-instructions.md): Quick reference workflows and patterns
- **[README.md](../README.md)**: Project overview, timeline, and stakeholders
- **[ARCHITECTURE.md](../ARCHITECTURE.md)**: Complete system architecture analysis (600 lines)
- **[EPICS-AND-STORIES.md](../EPICS-AND-STORIES.md)**: User stories extracted from legacy system (36 stories, 10 epics)

### Architecture Overview

**System Type**: Legacy Modernization - Oracle Forms 12c to Modern Web Stack

**Original System (ADMS-EI-SST)**:
- **Technology**: Oracle Forms 12c client/server on RHEL 7
- **Database**: Oracle 19c on IBM AIX
- **Purpose**: Employment Insurance appeals and Social Security Tribunal case management
- **Users**: Appeals Processing Centre staff (4 regional offices + NHQ)
- **Status**: End-of-life (Oracle Forms mainstream support ends 2027, RHEL 7 EOL June 2024)

**Target Architecture (Phase 1 - Employee Maintenance Module)**:
- **Backend**: .NET Core 8 Web API on Azure Gov Cloud
- **Frontend**: React 18 + TypeScript with Fluent UI
- **Database**: Azure SQL Database (initially, Oracle 19c via Entity Framework in transition)
- **Authentication**: Azure AD / Entra ID
- **CI/CD**: GitHub Actions with Azure DevOps integration
- **Deployment**: Azure App Service (Linux containers)

**Critical Architecture Patterns**:
- **Strangler Fig Pattern**: Gradually replace Oracle Forms module by module
- **API-First Design**: REST API independent of UI, enables future mobile apps
- **Government Cloud Compliance**: Azure Gov Cloud (Canada East/Central)
- **Bilingual Support**: English/French UI and data (Canadian requirement)
- **Offline Capability**: Service workers for degraded connectivity scenarios

### Project Structure

```
23-ei-dsst-rewrite/
 .github/
    copilot-instructions.md          # This file
    workflows/                       # GitHub Actions CI/CD
    CODEOWNERS                       # Code review assignments
 docs/
    ARCHITECTURE.md                  # Legacy system analysis (600 lines)
    EPICS-AND-STORIES.md            # 36 user stories, 10 epics
    api/                            # API specifications
    adr/                            # Architecture Decision Records
 src/
    backend/
       EmployeeMaintenance.API/    # .NET Core 8 Web API
       EmployeeMaintenance.Core/   # Business logic (domain models)
       EmployeeMaintenance.Data/   # EF Core data access
       EmployeeMaintenance.Tests/  # xUnit tests
    frontend/
        src/
           components/             # React components
           services/               # API clients
           state/                  # Redux state management
           i18n/                   # English/French translations
        public/
        package.json
 infra/
    terraform/                      # Azure infrastructure as code
    scripts/                        # Deployment automation
 tests/
    integration/                    # API integration tests
    e2e/                           # Playwright end-to-end tests
 README.md                           # Project overview
```

### Development Workflows

**Local Development Setup**:

```powershell
# Backend Setup (.NET Core 8)
cd src/backend/EmployeeMaintenance.API
dotnet restore
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "<connection-string>"
dotnet run  # Starts on https://localhost:5001

# Frontend Setup (React + TypeScript)
cd src/frontend
npm install
npm run dev  # Starts on http://localhost:3000
```

**Quick Commands**:
- **Backend**: 
  - Start: `dotnet run --project src/backend/EmployeeMaintenance.API`
  - Test: `dotnet test src/backend/EmployeeMaintenance.Tests`
  - Build: `dotnet build src/backend/EmployeeMaintenance.sln`
- **Frontend**:
  - Start: `npm run dev` (from `src/frontend/`)
  - Test: `npm test`
  - Build: `npm run build`
  - Lint: `npm run lint`
- **Infrastructure**:
  - Plan: `terraform plan` (from `infra/terraform/`)
  - Apply: `terraform apply`
- **E2E Tests**: `npx playwright test` (from `tests/e2e/`)

### Critical Code Patterns

#### Pattern 1: Entity Framework Repository Pattern

**Purpose**: Abstract data access for Oracle 19c database via Entity Framework Core

**Implementation**:
```csharp
// Core domain model
public class EmployeeProfile
{
    public int EmployeeId { get; set; }
    public string EmployeeName { get; set; }
    public string Email { get; set; }
    public string PreferredLanguage { get; set; }  // EN or FR
    public DateTime CreatedDate { get; set; }
    public bool IsActive { get; set; }
}

// Repository interface
public interface IEmployeeRepository
{
    Task<EmployeeProfile> GetByIdAsync(int employeeId);
    Task<IEnumerable<EmployeeProfile>> GetAllActiveAsync();
    Task<int> CreateAsync(EmployeeProfile employee);
    Task UpdateAsync(EmployeeProfile employee);
    Task DeactivateAsync(int employeeId);
}

// EF Core implementation
public class EmployeeRepository : IEmployeeRepository
{
    private readonly AdmsContext _context;
    
    public EmployeeRepository(AdmsContext context) => _context = context;
    
    public async Task<EmployeeProfile> GetByIdAsync(int employeeId)
    {
        return await _context.Employees
            .AsNoTracking()
            .FirstOrDefaultAsync(e => e.EmployeeId == employeeId);
    }
    
    // ... other methods
}
```

#### Pattern 2: Bilingual API Response (Canadian Government Requirement)

**Purpose**: Support English/French language switching without page reload

**Implementation**:
```csharp
// API Controller with language negotiation
[ApiController]
[Route("api/[controller]")]
public class EmployeesController : ControllerBase
{
    private readonly IEmployeeService _service;
    private readonly IStringLocalizer<EmployeesController> _localizer;
    
    [HttpGet("{id}")]
    public async Task<ActionResult<EmployeeDto>> GetEmployee(int id)
    {
        var employee = await _service.GetByIdAsync(id);
        if (employee == null)
            return NotFound(new { Message = _localizer["EmployeeNotFound"] });
        
        return Ok(employee);
    }
}
```

```typescript
// React component with i18n
import { useTranslation } from 'react-i18next';

const EmployeeForm: React.FC = () => {
  const { t, i18n } = useTranslation();
  
  const switchLanguage = () => {
    const newLang = i18n.language === 'en' ? 'fr' : 'en';
    i18n.changeLanguage(newLang);
  };
  
  return (
    <div>
      <h1>{t('employee.title')}</h1>
      <button onClick={switchLanguage}>
        {i18n.language === 'en' ? 'Franais' : 'English'}
      </button>
    </div>
  );
};
```

#### Pattern 3: Strangler Fig Migration - Feature Toggle

**Purpose**: Gradually replace Oracle Forms screens with React components

**Implementation**:
```csharp
// Feature flag service
public interface IFeatureFlagService
{
    Task<bool> IsModernEmployeeMaintenanceEnabled(string userId);
}

// API endpoint that routes to legacy or modern based on flag
[HttpGet("employee/{id}/ui")]
public async Task<ActionResult> GetEmployeeUI(int id)
{
    var user = User.Identity.Name;
    var useModernUI = await _featureFlags.IsModernEmployeeMaintenanceEnabled(user);
    
    if (useModernUI)
        return Redirect($"/modern/employee/{id}");  // React SPA
    else
        return Redirect($"/legacy/forms/employee/{id}");  // Oracle Forms
}
```

### Testing

**Test Structure**:
- **Unit Tests**: xUnit for .NET Core services and repositories
- **Integration Tests**: WebApplicationFactory for API testing
- **E2E Tests**: Playwright for full user workflows
- **Load Tests**: k6 for performance validation

**Running Tests**:
```powershell
# Backend unit tests
dotnet test src/backend/EmployeeMaintenance.Tests --logger "console;verbosity=detailed"

# Integration tests (requires test database)
dotnet test tests/integration --settings tests/integration/test.runsettings

# Frontend unit tests
npm test -- --coverage

# E2E tests with Playwright
npx playwright test --headed  # Run with visible browser
npx playwright test --project=chromium --project=firefox  # Cross-browser
npx playwright test --grep @smoke  # Run smoke tests only
```

### CI/CD Pipeline

**GitHub Actions Workflows**:
- **`ci-backend.yml`**: Build, test, security scan .NET API on every PR
- **`ci-frontend.yml`**: Build, lint, test React app on every PR
- **`deploy-dev.yml`**: Deploy to Azure Dev environment on merge to `develop`
- **`deploy-prod.yml`**: Deploy to Azure Prod environment on merge to `main` (requires approval)

**Deployment Process**:
1. PR triggers CI (build + test + security scans)
2. Merge to `develop` -> auto-deploy to Dev environment
3. Manual promotion to Staging -> runs smoke tests
4. Manual approval -> deploy to Production
5. Post-deployment: automated smoke tests + monitoring alerts

### Troubleshooting

#### Issue 1: Oracle Connection Timeout

**Symptom**: EF Core throws timeout exceptions when connecting to Oracle 19c on IBM AIX  
**Cause**: Network latency or AIX firewall rules blocking Azure connections  
**Solution**: 
```csharp
// Increase command timeout in DbContext
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder.UseOracle(connectionString, options =>
    {
        options.CommandTimeout(120);  // 2 minutes
        options.UseOracleSQLCompatibility("11");  // For Oracle 19c
    });
}
```

#### Issue 2: Bilingual Data Mixed in Database

**Symptom**: English descriptions saved to French fields (legacy data issue)  
**Cause**: Oracle Forms did not enforce language column separation  
**Solution**: Create database migration to separate bilingual fields:
```csharp
public partial class SeparateBilingualFields : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.AddColumn<string>("DescriptionEN", "Employees");
        migrationBuilder.AddColumn<string>("DescriptionFR", "Employees");
        
        // SQL to split existing Description column based on language detection
        migrationBuilder.Sql(@"
            UPDATE Employees
            SET DescriptionEN = CASE WHEN PreferredLanguage = 'EN' THEN Description END,
                DescriptionFR = CASE WHEN PreferredLanguage = 'FR' THEN Description END
        ");
    }
}
```

#### Issue 3: React Build Fails in Azure Pipeline

**Symptom**: `npm run build` fails with "JavaScript heap out of memory"  
**Cause**: Default Node.js memory limit (512MB) insufficient for large TypeScript project  
**Solution**: Increase Node.js memory in pipeline YAML:
```yaml
- task: Npm@1
  inputs:
    command: 'custom'
    customCommand: 'run build'
  env:
    NODE_OPTIONS: '--max-old-space-size=4096'  # 4GB
```

#### Issue 4: Azure AD Authentication Loop

**Symptom**: Users redirected back to login after successful authentication  
**Cause**: MSAL.js redirect URI mismatch or missing Azure AD app permissions  
**Solution**: Verify Azure AD app registration:
```typescript
// Ensure redirect URI matches exactly (including trailing slash)
const msalConfig = {
  auth: {
    clientId: process.env.REACT_APP_AZURE_AD_CLIENT_ID,
    authority: `https://login.microsoftonline.com/${process.env.REACT_APP_AZURE_AD_TENANT_ID}`,
    redirectUri: 'https://adms-ei-sst.canada.ca/'  // Must match Azure AD exactly
  }
};
```

#### Issue 5: Copilot Suggests Insecure Patterns

**Symptom**: GitHub Copilot suggests storing connection strings in code or disabling HTTPS  
**Cause**: Training data includes insecure examples from public repositories  
**Solution**: **ALWAYS reject insecure suggestions**:
- [NEVER]: `builder.Services.AddDbContext<>(options => options.UseOracle("connString"))`
- [ALWAYS]: `builder.Services.AddDbContext<>(options => options.UseOracle(Configuration.GetConnectionString("DefaultConnection")))`
- [NEVER]: `app.UseHttpsRedirection();  // Commented out for local dev`
- [ALWAYS]: Keep HTTPS enforcement, use dev certificates: `dotnet dev-certs https --trust`

### Performance Optimization

**Frontend**:
- **Code Splitting**: Lazy load routes with `React.lazy()` and `Suspense`
- **Memoization**: Use `useMemo` and `React.memo` for expensive components
- **Bundle Size**: Keep initial bundle < 250KB (use Webpack Bundle Analyzer)
- **Image Optimization**: Use WebP format, responsive images with `<picture>`

**Backend**:
- **Database Query**: Always use `.AsNoTracking()` for read-only queries
- **Caching**: Redis for frequently accessed lookup data (office codes, profile types)
- **Pagination**: Implement skip/take pattern, limit results to 50 per page
- **Connection Pooling**: Configure EF Core connection pool size based on load testing

**Azure Optimization**:
- **App Service Plan**: Start with P1V2 (2 cores, 3.5GB RAM), scale to P2V2 if needed
- **Application Insights**: Enable adaptive sampling to reduce telemetry costs
- **CDN**: Use Azure CDN for static React assets (CSS, JS, images)

---

**For legacy system analysis, see [ARCHITECTURE.md](../ARCHITECTURE.md)**  
**For user stories and requirements, see [EPICS-AND-STORIES.md](../EPICS-AND-STORIES.md)**


---

### Skills in This Project

`powershell
Get-ChildItem ".github/copilot-skills" -Filter "*.skill.md" | Select-Object Name
`

Read `00-skill-index.skill.md` to see what agent skills are available for this project.
Match the user's trigger phrase to the skill, then read that skill file in full.
