# NAME

VolSurface::Utils - A class that handles several volatility related methods

# SYNOPSIS

A class that handles several volatility related methods such as gets strikes from a certain delta point, gets delta from a certain vol point etc.

    use VolSurface::Utils;

    my ($strike, $atm_vol, $t, $spot, $r_rate, $q_rate, $premium_adjusted) = (); ## initialize args
    my $delta = get_delta_for_strike({
        strike           => $strike,
        atm_vol          => $atm_vol,
        t                => $t,
        spot             => $spot,
        r_rate           => $r_rate,
        q_rate           => $q_rate,
        premium_adjusted => $premium_adjusted
    });

# EXPORT

get\_delta\_for\_strike

get\_strike\_for\_spot\_delta

get\_ATM\_strike\_for\_spot\_delta

get\_moneyness\_for\_strike

get\_strike\_for\_moneyness

get\_1vol\_butterfly

get\_2vol\_butterfly

# METHODS

## get\_delta\_for\_strike

Returns the delta (spot delta or premium adjusted spot delta) correspond to a particular strike with set of parameters such as atm volatility, time in year, spot level, rates

    my $delta = get_delta_for_strike({
        strike           => $strike,
        atm_vol          => $atm_vol,
        t                => $t,
        spot             => $spot,
        r_rate           => $r_rate,
        q_rate           => $q_rate,
        premium_adjusted => $premium_adjusted,
        forward          => $forward
    });

Spot delta of an option is the percentage of the foreign notional one must buy when selling the option to hold a hedged position in the spot markets.

Premium adjusted spot delta is the spot delta which adjusted to take care of the correction induced by payment of the premium in foreign currency, which is the amount by which the delta hedge in foreign currency has to be corrected.

## get\_strike\_for\_spot\_delta

Returns the strike corresponds to a particular delta (spot delta or premium adjusted spot delta) with a set of parameters such as option type, atm vol, time in year, rates and spot level.

    my $strike = get_strike_for_spot_delta({
        delta            => $delta,
        option_type      => $option_type,
        atm_vol          => $atm_vol,
        t                => $t,
        r_rate           => $r_rate,
        q_rate           => $q_rate,
        spot             => $spot,
        premium_adjusted => $premium_adjusted
    });

Calculation of strike depends on which type of delta we have. Delta provided must be on \[0,1\].

## get\_ATM\_strike\_for\_spot\_delta

Returns the ATM strike that satisifies straddle Delta neutral.

    my $atm_strike = get_ATM_strike_for_spot_delta({
        atm_vol => $atm_vol,
        t => $t,
        r_rate => $r_rate,
        q_rate => $q_rate,
        spot => $spot,
        premium_adjusted => $premium_adjusted,
    });

The ATM volatility quoted in the market is that of a zero delta
straddle, whose strike, for each given expiry, is chosen so that
a put and a call have the SAME delta but with different signs.
No delta hedge is needed when trading this straddle.

The ATM volatility for the expiry T is the volatility where the ATM strike K must satisfy the following condition:
Delta Call =  - Delta Put

The ATM strike is the strike correspond to this ATM volatility.

## get\_moneyness\_for\_strike

Returns the corresponding moneyness point for a given strike.

    my $moneyness = get_moneyness_for_strike({
        strike => $strike,
        spot => $spot,
    });

## get\_strike\_for\_moneyness

Returns the corresponding strike value for a given moneyness point.

    my $strike = get_strike_for_moneyness({
           spot => $spot,
           moneyness => $moneyness
       });

## get\_2vol\_butterfly

Returns the two vol butterfly that satisfy the abitrage free constraint.

    my $bf = get_2vol_butterfly($spot, $tiy,$delta, $atm, $rr, $bf, $r, $d, $premium_adjusted, $bf_style);

DESCRIPTION:
There are two different butterfly vol:

\-The first one is 2 vol butterfly which is the quoted butterfly that appear in interbank market (vwb= 0.5(Sigma(call)+SigmaP(Put))- Sigma(ATM)).

\-The second one is 1 vol butterfly which is the butterfly volatility that consistent with market standard conventions of trading the butterfly  strategies (some paper called it market strnagle volatility).

The market standard conventions for trading the butterfly is price the strangle with one unique volatility whereas with the first butterfly convention(ie the quoted butterfly vol), we will price the strangle with two volatility.

There is possible arbitrage opportunities that might result from the inconsistency caused by the above quoting mechanism.

Hence, in practice, we need to build a volatility smile so that the price of the two options strangle based on the volatility surface that we build will have same price as the one from the market conventional butterfly trading(ie with one unique volatility).

The consistent constraint that need to hold in building surface is as shown as follow :
C(K\_25C, Vol\_K\_25C) + P(K\_25P, Vol\_K\_25P) = C(K\_25C, Vol\_market\_conventional\_bf) + P(K\_25P, Vol\_market\_conventional\_bf)

The first step in building the abitrage free volatility smile is to determine an equivalent butterfly which will combines with all the ATM and RR vol to yields a volatility smile that satisfies the above constraint .

This equivalent butterfly which is also named as two vol butterfly or smiled butterfly can be found numerically.

This is only needed if the butterfly is the 1 vol butterfly from the market without any adjustment yet. If vol smile is abitrage free, hence their BF is already adjusted accordingly to fullfill the abitrage free constraints, hence no adjustment needed on the BF.

As this process gone through a numerical procedures, hence the result might be slightly different when compare with other vendor as they might used different approach to get the relevant result.

## get\_1vol\_butterfly

Returns the 1 vol butterfly which is the butterfly volatility that consistent with market standard conventions of trading the butterfly strategies (some paper called it market strnagle volatility)

    my $bf_1vol = get_1vol_butterfly({
        spot             => $volsurface->underlying->spot,
        tiy              => $tiy,
        delta            => 0.25,
        call_vol         => $smile->{25},
        put_vol          => $smile->{75},
        atm_vol          => $smile->{50},
        bf_1vol          => 0,
        r                => $volsurface->underlying->interest_rate_for($tiy),
        q                => $volsurface->underlying->dividend_rate_for($tiy),
        premium_adjusted => $volsurface->underlying->{market_convention}->{delta_premium_adjusted},
        bf_style         => '2_vol',
    });

# AUTHOR

Binary.com, `<support at binary.com>`

# BUGS

Please report any bugs or feature requests to `bug-volsurface-utils at rt.cpan.org`, or through
the web interface at [http://rt.cpan.org/NoAuth/ReportBug.html?Queue=VolSurface-Utils](http://rt.cpan.org/NoAuth/ReportBug.html?Queue=VolSurface-Utils).  I will be notified, and then you'll
automatically be notified of progress on your bug as I make changes.

# SUPPORT

You can find documentation for this module with the perldoc command.

    perldoc VolSurface::Utils

You can also look for information at:

- RT: CPAN's request tracker (report bugs here)

    [http://rt.cpan.org/NoAuth/Bugs.html?Dist=VolSurface-Utils](http://rt.cpan.org/NoAuth/Bugs.html?Dist=VolSurface-Utils)

- AnnoCPAN: Annotated CPAN documentation

    [http://annocpan.org/dist/VolSurface-Utils](http://annocpan.org/dist/VolSurface-Utils)

- CPAN Ratings

    [http://cpanratings.perl.org/d/VolSurface-Utils](http://cpanratings.perl.org/d/VolSurface-Utils)

- Search CPAN

    [http://search.cpan.org/dist/VolSurface-Utils/](http://search.cpan.org/dist/VolSurface-Utils/)

# ACKNOWLEDGEMENTS

# LICENSE AND COPYRIGHT

Copyright 2015 Binary.com.
