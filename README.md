# Fith

*Non-functioning work in progress*

Fith is a Forth-like language implemented in Icon that extends Forth syntax to encompass multiple results.  Most words are named in a manner familiar to Icon programmers, although a facility might be added to create a more Forth-centric dialect (perhaps even encompassing other dialects, e.g., a Python-like dialect).

When an Icon program starts, the \&main co-expression is activated and procedure main is called.  When a Fith invocation begins, \&main creates a co-expression and assigns it to a variable  produced by the fith procedure, in a manner similar to this, implementing the publisher-subscriber pattern such that a variable is read-only outside of the C (co-expression) that originally sets it:

```
  procedure fith()
    return wora(fith, &main)
  end
  
  # WORA - Writeable-by-One, Readable-by-All
  procedure wora(id)
    local owner
    static s_ownerT, s_valueT
    initial {
      s_ownerT := table()
      s_valueT := table()
    }
    if /id then fail
    owner := \s_ownerT[id]
    if /owner then {
      # WORA is settable
      s_ownerT[id] := &current
      suspend s_valueT[id]
    } else if owner === &current then {
      # WORA is resettable by owner
      suspend s_valueT[id]
    } else {
      # produce an unassignable copy of WORA
      suspend .(s_valueT[id])
    }
  end
  
  procedure main(args)
    wora(fith) := create "fith(): fith is " || image(fith())
    write("&main: wora(fith) = ", image(wora(fith)))
    write(@wora(fith))
  end
```
