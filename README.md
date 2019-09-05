### gcss
---
https://github.com/yosssi/gcss

```go
// parse.go

func parse(lines []string) (<-chan element, <-chan error) {
  elemc := make(chan element, len(lines))
  errc := make(chan error)
  
  go func() {
    i := 0
    l := len(lines)
    
    for i < l {
      ln := newLine(i+1, lines[i])
      i++
      
      if ln.isEmpty() {
        continue
      }
      
      if ln.isTopIndent() {
        elem, err := newElement(ln, nil)
        
        if err != nil {
          errc <- err
          return
        }
        
        if err := appendChildren(elem, lines, &i, l); err != nil {
          errc <- err
          return
        }
        
        elemc <- elem
      }
    }
    
    close(elemc)
  }()
  
  return elemc, errc
}

func appendChildren(parent element, lines []string, i *int, l int) error {
  for *i < l {
    ln := newLine(*i+1, lines[*i])
    
    if ln.isEmpty() {
      *i++
      return nil
    }
    
    ok, err := ln.childOf(parent)
    
    if err != nil {
      return err
    }
    
    if !ok {
      return nil
    }
    
    child, err := newElement(ln, parent)
    
    if err != nil {
      return err
    }
    
    parent.AppendChild(child)
    
    *i++
    
    if err := appendChildren(child, lines, i, l); err != nil {
      return err
    }
  }
  
  return nil
}

func formatLF(s string) string {
  return strings.Replace(strings.Replace(s, crlf, lf, -1), cr, lf, -1)
}

```

```
```

```
```


