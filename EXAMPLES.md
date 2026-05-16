# Examples

Real-world examples demonstrating task-driven skills, goal-driven orchestrators, and oversight-driven supervisors. Each example shows what LLMs commonly do wrong and how to fix it.

---

## 1. Think Before Coding

### Example 1: Hidden Assumptions

**User Request:** "Add a feature to export user data"

**❌ What LLMs Do (Wrong Assumptions)**

```python
def export_users(format='json'):
    """Export all users to a file."""
    users = User.query.all()
    
    if format == 'json':
        with open('users.json', 'w') as f:
            json.dump([u.to_dict() for u in users], f)
    elif format == 'csv':
        with open('users.csv', 'w') as f:
            writer = csv.DictWriter(f, fieldnames=['id', 'email', 'name'])
            writer.writeheader()
            writer.writerows([u.to_dict() for u in users])
    
    return f"Exported {len(users)} users"
```

**Problems:**
- Assumed it should export ALL users (what about pagination? privacy?)
- Assumed file location without asking
- Assumed which fields to include
- Assumed CSV fieldnames without checking actual data structure

**✅ What Should Happen (Surface Assumptions)**

```
Before implementing, I need to clarify:

1. **Scope**: Export all users or filtered subset? (privacy implications)
2. **Format**: You mentioned "export" - did you mean:
   - Download file in browser?
   - Background job with email notification?
   - API endpoint returning data?
3. **Fields**: Which user fields? (some might be sensitive)
4. **Volume**: How many users typically? (affects approach)

Simplest approach: Add an API endpoint that returns paginated JSON.
Would need more info for file-based exports.

What's your preference?
```

### Example 2: Multiple Interpretations

**User Request:** "Make the search faster"

**❌ What LLMs Do (Pick Silently)**

```python
# Adds caching, database indexes, and async processing
# without asking which "faster" matters
@lru_cache(maxsize=1000)
async def search(query: str) -> List[Result]:
    # ... 200 lines of optimization code
```

**✅ What Should Happen**

```
"Make the search faster" could mean:

1. **Faster response time** (< 100ms instead of 500ms)
   - Add database indexes
   - Cache frequent queries
   - Estimated effort: 2 hours

2. **Handle more concurrent searches** (throughput)
   - Add async/await
   - Connection pooling
   - Estimated effort: 4 hours

3. **Faster perceived speed** (UX)
   - Show partial results immediately
   - Progressive loading
   - Estimated effort: 3 hours

Current search takes ~500ms for typical queries. 
Which aspect matters most for your use case?
```

---

## 2. Simplicity First

### Example 1: Over-abstraction

**User Request:** "Add a function to calculate discount"

**❌ What LLMs Do (Overengineered)**

```python
from abc import ABC, abstractmethod
from enum import Enum
from typing import Protocol, Union
from dataclasses import dataclass

class DiscountStrategy(ABC):
    @abstractmethod
    def calculate(self, amount: float) -> float:
        pass

class PercentageDiscount(DiscountStrategy):
    def __init__(self, percentage: float):
        self.percentage = percentage
    
    def calculate(self, amount: float) -> float:
        return amount * (self.percentage / 100)

class FixedDiscount(DiscountStrategy):
    def __init__(self, fixed_amount: float):
        self.fixed_amount = fixed_amount
    
    def calculate(self, amount: float) -> float:
        return min(self.fixed_amount, amount)

@dataclass
class DiscountConfig:
    strategy: DiscountStrategy
    min_purchase: float = 0.0
    max_discount: float = float('inf')

class DiscountCalculator:
    def __init__(self, config: DiscountConfig):
        self.config = config
    
    def apply_discount(self, amount: float) -> float:
        if amount < self.config.min_purchase:
            return 0
        discount = self.config.strategy.calculate(amount)
        return min(discount, self.config.max_discount)

# Usage requires 30+ lines of setup for a simple calculation
```

**✅ What Should Happen (Simple)**

```python
def calculate_discount(amount: float, percent: float) -> float:
    """Calculate discount amount. percent should be 0-100."""
    return amount * (percent / 100)

# Usage
discount = calculate_discount(100.0, 10.0)  # $10 off
```

**When to add complexity:** Only when you actually need multiple discount types. If that requirement comes later, refactor then.

### Example 2: Speculative Features

**User Request:** "Save user preferences to database"

**❌ What LLMs Do (Unnecessary Features)**

```python
class PreferenceManager:
    def __init__(self, db, cache=None, validator=None):
        self.db = db
        self.cache = cache or InMemoryCache()
        self.validator = validator or DefaultValidator()
    
    def save(self, user_id: int, prefs: dict, 
             merge: bool = True, 
             validate: bool = True,
             notify: bool = False) -> bool:
        """Save preferences with optional merging, validation, and notifications."""
        
        if validate:
            errors = self.validator.validate(prefs)
            if errors:
                raise ValidationError(errors)
        
        existing = self.get(user_id) if merge else {}
        
        if merge:
            existing.update(prefs)
            prefs = existing
        
        self.db.execute(
            "UPDATE users SET preferences = ? WHERE id = ?",
            (json.dumps(prefs), user_id)
        )
        
        if self.cache:
            self.cache.set(f"prefs:{user_id}", prefs)
        
        if notify:
            self.notify_preference_change(user_id, prefs)
        
        return True
    
    def notify_preference_change(self, user_id: int, prefs: dict):
        # Another 30 lines for a feature nobody asked for
        pass
```

**✅ What Should Happen (Just What Was Asked)**

```python
def save_preferences(db, user_id: int, preferences: dict):
    """Save user preferences to database."""
    db.execute(
        "UPDATE users SET preferences = ? WHERE id = ?",
        (json.dumps(preferences), user_id)
    )
```

**Add later if needed:** Caching (when performance matters), validation (when bad data appears), merging (when requirement emerges).

---

## 3. Surgical Changes

### Example 1: Drive-by Refactoring

**User Request:** "Fix the bug where empty emails crash the validator"

**❌ What LLMs Do (Too Much)**

```diff
  def validate_user(user_data):
-     # Check email format
-     if not user_data.get('email'):
+     """Validate user data."""
+     email = user_data.get('email', '').strip()
+     
+     # Validate email
+     if not email:
          raise ValueError("Email required")
-     
-     # Basic email validation
-     if '@' not in user_data['email']:
+     if '@' not in email or '.' not in email.split('@')[1]:
          raise ValueError("Invalid email")
      
-     # Check username
-     if not user_data.get('username'):
+     # Validate username
+     username = user_data.get('username', '').strip()
+     if not username:
          raise ValueError("Username required")
+     if len(username) < 3:
+         raise ValueError("Username too short")
+     if not username.isalnum():
+         raise ValueError("Username must be alphanumeric")
      
      return True
```

**Problems:**
- "Improved" email validation beyond the bug fix
- Added username validation nobody asked for
- Changed comments
- Added docstring

**✅ What Should Happen (Surgical)**

```diff
  def validate_user(user_data):
      # Check email format
-     if not user_data.get('email'):
+     email = user_data.get('email', '')
+     if not email or not email.strip():
          raise ValueError("Email required")
      
      # Basic email validation
-     if '@' not in user_data['email']:
+     if '@' not in email:
          raise ValueError("Invalid email")
      
      # Check username
      if not user_data.get('username'):
          raise ValueError("Username required")
      
      return True
```

**Only changed:** The specific lines that fix empty email handling.

### Example 2: Style Drift

**User Request:** "Add logging to the upload function"

**❌ What LLMs Do (Reformat Everything)**

```diff
- def upload_file(file_path, destination):
+ def upload_file(file_path: str, destination: str) -> bool:
+     """Upload file to destination with logging."""
+     logger.info(f"Uploading {file_path} to {destination}")
+     
      try:
-         with open(file_path, 'rb') as f:
-             data = f.read()
-         
-         response = requests.post(destination, files={'file': data})
-         
-         if response.status_code == 200:
-             return True
-         else:
-             return False
+         with open(file_path, "rb") as f:
+             data = f.read()
+         response = requests.post(destination, files={"file": data})
+         success = response.status_code == 200
+         
+         if success:
+             logger.info(f"Upload successful: {file_path}")
+         else:
+             logger.error(f"Upload failed: {file_path}, status={response.status_code}")
+         
+         return success
      except Exception as e:
-         print(f"Error: {e}")
+         logger.exception(f"Upload error for {file_path}: {e}")
          return False
```

**Problems:**
- Changed quote style ('' to "")
- Added type hints nobody asked for
- Added docstring
- Reformatted whitespace
- Changed boolean return logic

**✅ What Should Happen (Match Existing Style)**

```diff
+ import logging
+ 
+ logger = logging.getLogger(__name__)
+ 
  def upload_file(file_path, destination):
+     logger.info(f'Starting upload: {file_path}')
      try:
          with open(file_path, 'rb') as f:
              data = f.read()
          
          response = requests.post(destination, files={'file': data})
          
          if response.status_code == 200:
+             logger.info(f'Upload successful: {file_path}')
              return True
          else:
+             logger.error(f'Upload failed: {file_path}, status={response.status_code}')
              return False
      except Exception as e:
-         print(f"Error: {e}")
+         logger.exception(f'Upload error: {file_path}')
          return False
```

**Matched:** Single quotes, no type hints, existing boolean pattern, spacing style.

---

## 4. Task-Driven Execution

### Example 1: Bounded Task Contract

**Assigned Task:** "In `tests/test_sessions.py`, add a regression test that changing a password invalidates existing sessions. Do not modify production code."

**❌ What LLMs Do (Act Like an Orchestrator)**

```diff
+ def rotate_session_secret(user_id):
+     # new production behavior that was not requested
+     ...
+
+ def invalidate_all_sessions(user_id):
+     # broad auth rewrite
+     ...
```

**Problems:**
- Expanded a test-writing task into a production-code task
- Touched auth behavior without a failing test contract
- Made review harder because the task boundary disappeared

**✅ What Should Happen (Execute the Task Contract)**

```python
def test_password_change_invalidates_existing_session(client, user):
    session_token = client.login(user.email, 'old-password')

    client.change_password(user.id, 'old-password', 'new-password')

    response = client.get('/me', headers={'Authorization': f'Bearer {session_token}'})
    assert response.status_code == 401
```

**Report:** Added the requested regression test only. It currently fails because production code does not invalidate sessions on password change. Next task should implement that behavior.

### Example 2: Goal Sent to a Skill

**User Request:** "Fix the authentication system"

**❌ What LLMs Do (Pretend the Goal Is a Task)**

```
I'll review the auth system, improve session handling, add validation, refactor login,
and update tests.
```

**✅ What Should Happen (Reject Vague Scope)**

```
"Fix the authentication system" is a goal, not a bounded skill task.
Please provide the specific bug, expected behavior, files in scope, and verification
command, or route this through an orchestrator to decompose it into tasks.
```

---

## 5. Orchestrator Goal-Driven Execution

### Example 1: Vague vs. Verifiable

**User Goal:** "Fix the authentication system"

**❌ What LLMs Do (Vague Approach)**

```
I'll fix the authentication system by:
1. Reviewing the code
2. Identifying issues
3. Making improvements
4. Testing the changes

[Proceeds to make changes without clear success criteria]
```

**✅ What Should Happen (Goal Decomposition)**

```
To "fix authentication," I need success criteria.

If the issue is "users stay logged in after password change":

Plan:
1. Task: Write failing regression test for password-change session invalidation
   Owner: test-writing skill
   Verify: Test fails for the current bug

2. Task: Implement session invalidation on password change
   Owner: implementation skill
   Verify: Regression test passes

3. Task: Check edge cases: multiple active sessions and concurrent changes
   Owner: test-writing skill
   Verify: Additional tests pass

4. Task: Run existing auth tests
   Owner: verification skill
   Verify: Full auth suite green

What specific auth issue are we solving?
```

### Example 2: Multi-Step with Verification

**User Goal:** "Add rate limiting to the API"

**✅ What Should Happen (Incremental Dispatch)**

```
Plan for rate limiting:

1. Task: Add basic in-memory rate limiting to one endpoint
   Verify:
   - Test: 100 requests -> first 10 succeed, rest get 429
   - Manual: curl endpoint 11 times, see rate limit error

2. Task: Extract rate limiting to middleware
   Verify:
   - Test: Rate limits apply to /users and /posts
   - Existing endpoint tests still pass

3. Task: Add Redis backend only if multi-server deployment is required
   Verify:
   - Test: Two app instances share rate limit counter

Each task is independently verifiable and bounded.
```

---

## 6. Supervisor Oversight and Learning

### Example 1: Independent Diff Review

**Executor Claim:** "Implemented the requested empty-email validator fix."

**Diff Contains:**

```diff
  def validate_user(user_data):
-     if not user_data.get('email'):
+     email = user_data.get('email', '').strip()
+     if not email:
          raise ValueError("Email required")
+     if len(user_data.get('username', '')) < 3:
+         raise ValueError("Username too short")
```

**✅ Supervisor Finding**

```
The email fix is in scope, but the new username length validation is unrelated.
Request a follow-up patch that removes the username change before approval.
```

### Example 2: Lean Skill Improvement

**Repeated Observation:** Three runs added unrelated validation while fixing a narrow validator bug.

**❌ Bad Skill Update**

```
Add two pages of validator-specific instructions covering email, username, phone,
address, passwords, profile data, and every possible future field.
```

**✅ Good Skill Update**

```
When fixing a validator bug, change only the validation path needed to reproduce
and fix the reported failure. Do not add validation for unrelated fields.
```

This update is short, durable, and broadly useful.

---

## Anti-Patterns Summary

| Layer | Anti-Pattern | Fix |
|-------|--------------|-----|
| Skill | Silently assumes file format, fields, or scope | List assumptions explicitly; ask or report a blocker |
| Skill | Strategy pattern for a single discount calculation | Use one function until complexity is actually needed |
| Skill | Reformats quotes and adds type hints while fixing a bug | Only change lines that fix the reported issue |
| Skill | Treats "fix authentication" as a bounded task | Ask for a task contract or route to an orchestrator |
| Orchestrator | Dispatches "improve auth" directly to a skill | Define success criteria, decompose into bounded tasks, verify each one |
| Supervisor | Accepts executor claims without checking the diff | Review plan, diff, tests, and final claims independently |
| Supervisor | Adds every lesson to the skill forever | Promote repeated durable lessons; prune stale or duplicate rules |

## Key Insight

The "overcomplicated" examples aren't obviously wrong—they follow design patterns and best practices. The problem is **timing and layer confusion**:

- Skills should solve today's assigned task simply.
- Orchestrators should manage goals, dependencies, and verification loops.
- Supervisors should catch drift and improve future runs without bloating the skill.

**Good agentic coding keeps the right responsibility in the right layer.**
