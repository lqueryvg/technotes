# Perl

## Debugger
```
n               # next
Enter           # repeat previous next
T               # where
x %var          # dump var
b func          # set break in func
b module::func  # but only after module has been loaded
L               # list breakpoints
V               # dump variables in main package
```

## Profiling/Performance
```
perl -d:DProf <progname>  # generates tmon.out
dprofpp
```

## Carp

    carp            # warn
    cluck           # warn + stack
    croak           # die
    confess         # die + stack

## Message logging with exceptions
```
sub logit {
  my ($str) = @_;
  my $caller = (caller(1))[3];
  print "$caller(): $str\n";
}
sub bad_lower {
  logit("called");
  die "bad_lower gone bad";   # this will bubble up the stack
  # Carp::confess 'gone bad'; # see whole stack trace
  logit("returning");
}
sub bad_upper {
  logit("called");
  bad_lower();
  logit("returning");
}
eval { bad_upper() };
print "exception is '$@'\n";
```
Prints:
```
main::bad_upper(): called
main::bad_lower(): called
exception is 'bad_lower gone bad at ./test.pl line 55.
```

## Hash slices

    my %ss;
    @ss{qw/fred barney dino/} = @values;

### With a reference

    my $rec = {};
    my @fields = qw/cmd resource subType/;
    @{$rec}{@fields} = split(/\s+/, $line);

#### Inside another structure

    @{$array{$id}}{qw/width site rank/} = ($width, $site, $rank);  # slice

## `use` vs `require`

`require`
- run time
- no import
- with bareword assumes `.pm` extension

`use`
- compile time
- equivalent to:
```
    BEGIN { require Module; Module->import( LIST ); }
```
## eval forms

    eval "" # parsed at run time, executed in context of current running program
    eval {} # parsed once, at compile time, traps run time exceptions

## `AUTOLOAD`
sub `AUTOLOAD` is called for any sub not defined.
- it sets `$AUTOLOAD` to name of sub called (including "package ::")

## `return`
Use bare `return` for failure because:
- `return undef` in a list context will return a list with 1 `undef` element - which evaluates to true.

## File IO

Use 3 argument form of `open`.

    open( $fh, '>output.txt' );          # not ok
    open( $fh, q{>}, 'output.txt' );     # ok

    use IO::File;
    my $fh = IO::File->new( 'output.txt', q{>} ); # even better!

It's clearer to define the input mode of the file, as in the difference between these two:

    open( $fh, 'foo.txt' );       # BAD: Reader must think what default mode is
    open( $fh, '<', 'foo.txt' );  # GOOD: Reader can see open mode


### Iterate lines

    for my $line ( <$file_handle> ){ thing($line) }      # BAD
    while ( my $line = <$file_handle> ){ thing($line) }  # GOOD

Because `<>` in list context reads all the lines it can
into a temporary list, then iterates over that.
In a scalar context, it just reads the next line.

## Named parameter pattern

    thefunc(INCREMENT => "20s", START => "+5m", FINISH => "+30m");
    sub thefunc {
        my %args = ( 
            INCREMENT   => '10s', 
            FINISH      => 0, 
            START       => 0, 
            @_,         # argument pair list goes here
        );
        if ($args{INCREMENT}  =~ /m$/ ) { ..... }
    }

Or...


    my (%args) = @_;
    my ($custom_fields_aref, $attrs_href) =
      @args{qw/custom_fields_aref attrs_href/};     # slice


## Misc

    scalar @arr                 # array length
    perldoc -l Tie::RefHash     # locate module
    eq                          # string equality
    ==                          # numeric equality
    m                           # multi-line regex
    x                           # (xtend) allow spaces & comments in regex
    local                       # use to save global variables inside a block
    BEGIN { }                   # compile & run before rest of compilation
    END { }                     # called when prog ends, if through an exception (e.g. die)

