# rhodopsin_snake_plot

Script for snake plot depiction of rhodopsin data in ggplot

### Rhodopsin snake plot coloured by site
![Rhodopsin snake plot coloured by site](https://github.com/alexvannynatten/rhodopsin_snake_plot/blob/4acc12b8091c1a71e0870fa4faebc929526dd1b8/Rhodpsin_snake_plot_1.png)

### Rhodopsin snake plot depicting variation in structure and function across sites
![Rhodopsin snake plot depicting variation in structure and function across sites](https://github.com/alexvannynatten/rhodopsin_snake_plot/blob/4acc12b8091c1a71e0870fa4faebc929526dd1b8/Rhodpsin_snake_plot_2.png)

### Rhodopsin snake plot depicting variation in dN/dS and locations of known substitutions associated with retinitis pigmentosa
![Rhodopsin snake plot depicting variation in dN/dS and locations of known substitutions associated with retinitis pigmentosa](https://github.com/alexvannynatten/rhodopsin_snake_plot/blob/4acc12b8091c1a71e0870fa4faebc929526dd1b8/Rhodpsin_snake_plot_3.png)

## R code:

```r
library(tidyverse)

# File contains x/y positions of each site in rhodopsin
snake_rho <- read.csv('rho_snake_plot.csv')

# Plots rhodopsin snake plot with rainbow colour by site
ggplot(snake_rho, aes(x = X_axis, y = Y_axis)) + 

	geom_point(aes(fill = Site), 
		shape = 21, size = 3) + 

	geom_text(aes(label=ifelse(Site %in% 
	c(1, seq(50,350,50), 348),as.character(Site),'')),
	hjust = 1.2, vjust = -0.2, colour = 'grey60', size = 3) + 

	theme_void() + 
	scale_fill_distiller(palette = "greens") + 
	theme(legend.position = "none")

ggsave('Rhodpsin_snake_plot_1.png')

#####################################################################
# Some examples of data that can be visualized on the snake plot
#####################################################################

# Structural data from Van Nynatten et al. 2021
rho_struct_data <- read_csv('rho_strunc_func_data.csv')

# Combines the data with the snake plot data
rho_struct_snake <- snake_rho %>% 
	left_join(rho_struct_data)

# Plots the data
ggplot(rho_struct_snake, aes(x = X_axis, y = Y_axis)) + 

	geom_point(aes(fill = Distance_to_chromophore_1U19,
				   size = RMSD_1U19_vs_3PQR), 
		shape = 21) + 

	geom_text(aes(label=ifelse(Site %in% 
	c(1, seq(50,350,50)),as.character(Site),'')),
	hjust = 1.2, vjust = -0.2, colour = 'grey60', size = 3) + 

	theme_void() + 
	scale_fill_viridis_c(direction = -1)

ggsave('Rhodpsin_snake_plot_2.png')

#####################################################################

# dN/dS and disease associated data from Hauser et al. 2016
rho_disease_data <- read_csv('rho_disease_dnds_data.csv')

# Combines the data with the snake plot data
rho_disease_snake <- snake_rho %>% 
	left_join(rho_disease_data) %>%
	mutate(RP_site = ifelse(is.na(ADRP_substitutions) == TRUE, '', 'yes'))

# Plots the data
ggplot(rho_disease_snake, aes(x = X_axis, y = Y_axis)) + 

	geom_point(aes(size = Mammal_dNdS, colour = RP_site), 
		shape = 21) + 

	geom_text(aes(label=ADRP_substitutions),
		hjust = -0.1, vjust = 0.4, size = 3, check_overlap = TRUE, colour = 'firebrick4') + 

	theme_void() + 
	scale_colour_manual(values = c('grey80', 'firebrick3'))

ggsave('Rhodpsin_snake_plot_3.png')

#####################################################################
